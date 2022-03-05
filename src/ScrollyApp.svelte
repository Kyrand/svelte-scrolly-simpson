<script>
  import { setContext, onMount } from 'svelte'
  import { getData, setColors, getMotion } from './utils.js'
  import { themes, regions, colors, datakeys } from './config.js'
  import { ScatterChart } from '@onsvisual/svelte-charts'
  import ONSHeader from './layout/ONSHeader.svelte'
  import ONSFooter from './layout/ONSFooter.svelte'
  import Header from './layout/Header.svelte'
  import Section from './layout/Section.svelte'
  import Media from './layout/Media.svelte'
  import Scroller from './layout/Scroller.svelte'
  import Filler from './layout/Filler.svelte'
  import Divider from './layout/Divider.svelte'
  import Map from './map/Map.svelte'
  import Em from './ui/Em.svelte'
  import Arrow from './ui/Arrow.svelte'

  import SimpsonsChart from './components/simpsons/SimpsonsChart.svelte'

  // STYLE CONFIG
  // Set theme globally (options are 'light' or 'dark')
  let theme = 'light'
  setContext('theme', theme)
  setColors(themes, theme)

  // SCROLLYTELLING CONFIG
  // Config
  const threshold = 0.65
  let animation = true
  let groupedByAge = false
  // State
  let index = []
  let indexPrev = []
  let key
  let image
  let prevImage
  onMount(() => {
    indexPrev = [...index]
  })

  $: imageUrl = 'img/' + image
  $: prevImageUrl = 'img/' + prevImage

  // Actions for CHART scroller
  // const chartActions = [
  //   () => {
  //     groupedByAge = false
  //   },
  //   () => {
  //     groupedByAge = true
  //   },
  // ]

  const chartActions = {
    ungrouped: () => {
      groupedByAge = false
    },
    grouped: () => {
      groupedByAge = true
    },
    legend: () => {},
  }

  let scrollyKey = ''
  // Reactive code to trigger CHART actions
  $: if (index[0] != indexPrev[0]) {
    console.log('Current key: ', key)
    // if (chartActions[+index[0]]) {
    //   chartActions[+index[0]]()
    // }
    if (chartActions[key]) {
      chartActions[key]()
    }
    indexPrev[0] = index[0]
  }
</script>

<!-- <ONSHeader filled={true} /> -->
<div class="app__wrapper">
  <Header bgimage="./img/simpsons-bg.png" bgfixed={true} theme="light" compact>
    <h1 class="_text-shadow bg-white">
      Visualizing Simpson's Paradox in Covid Vaccine Efficacy
    </h1>
    <!-- <p class="inset-medium text-big _text-shadow">... blah blah blah</p> -->
    <div class="_text-shadow" style="margin-top: 48px;">
      <Arrow color="_white" {animation}
        ><span class="_bg-white">Scroll to begin</span></Arrow
      >
    </div>
  </Header>

  <!-- <Filler theme="dark">
	<p class="text-big">
		This is a large text caption centred on a full-screen page
	</p>
</Filler> -->

  <Section>
    <h2>Simpson's Paradox</h2>
    <p>
      I recently came across a tweet which mentioned the likelihood that recent
      Covid vaccine efficacy stats coming out of Israel were an example of a
      famous statistical paradox generally attributed to the British
      statistician Edward Simpson but noticed by a number of predecessors (see
      the <a href="https://en.wikipedia.org/wiki/Simpson%27s_paradox"
        >Wikipedia entry</a
      > for details). Here's the tweet in question:
    </p>
    <div class="tweet__image">
      <img src="img/bak-coleman-tweet.png" alt="" />
    </div>
    <p>
      The paradox is that while the efficacy (we'll deal with how this is
      measured shortly) of the vaccine when assessed against the whole
      population of unvaccinated and fully vaccinated (67.5%) looks low, if we
      separate the population into over and under 50 years old the efficacy for
      each of those groups is considerably higher at 91.8% for under 50s and
      85.2% for over 50s.
    </p>
    <p>
      While it's very cool to have an example of a canonical paradox in the
      wild, one usually encountered as an interesting statistical quirk while
      studying the subject, in this case, as highlighted by Bak-Coleman, failure
      to appreciate the paradoxical results could result in detrimental policy
      decisions.
    </p>
    <p>
      If we take that low efficacy of 67.5% for the whole population it suggests
      the need of a booster shot for the currently vaccinated, diverting those
      vaccines from the unvaccinated. But if we deal with the over and under 50s
      separately their respective efficacies at 85.2% and 91.8% seem healthy
      enough and suggest not diverting first and second vaccine shots to boost
      the vaccinated with a third.
    </p>
    <p>
      So Simpson's paradox is not only interesting in its own right but in this
      case failure to appreciate it could do harm. Like most good paradoxes,
      Simpson's is hard to grasp and rather counterintuitive. It seems like a
      good candidate for some data visualization which can help people to that
      'aha' moment. There are various ones about but it occurred to me that this
      was a good case for creating an animated chart where you clearly see the
      morphing of the whole of the aggregated vaccinated and unvaccinated into
      age related groups and appreciate the way in which this affects the
      measured efficacies.
    </p>
  </Section>

  <Divider />

  <Section>
    <h2>Visualising Simpson's Paradox</h2>
    <p>
      The sections below will respond to the captions as you scroll down. The
      mode is set to splitscreen, with captions on the left on larger screens.
    </p>
  </Section>

  <Scroller
    {threshold}
    bind:index={index[0]}
    splitscreen={true}
    bind:key
    bind:image
    bind:prevImage
  >
    <div slot="background">
      <figure>
        <div class="col-wide height-full _middle">
          <div class="image" class:hidden={!image}>
            <!-- <img class="fade-out" src={prevImageUrl} alt="" /> -->
            {#if !!image}
              <img class="fade-in" src={imageUrl} alt="" />
            {/if}
          </div>
          <div class="chart" class:hidden={!!image}>
            <SimpsonsChart {groupedByAge} />
          </div>
        </div>
      </figure>
    </div>

    <div slot="foreground">
      <section key="foo" image="simpsons-legend.png">
        <div class="col-medium">
          <p>
            In the upcoming chart we'll use the colors red and green to mark the
            unvaccinated and vaccinated groups. Squares indicate an uninfected
            sample, triangles a severely infected sample.
          </p>
        </div>
      </section>
      <section key="baa" image="simpsons-legend-u50.png">
        <div class="col-medium">
          <p>
            We'll place a black circle at the center of samples taken from the
            under 50s group to clearly divide the over and under 50s.
          </p>
        </div>
      </section>
      <section key="baa" image="simpsons-efficacy.png">
        <div class="col-medium">
          <p>
            Efficacy is the key metric when calculating the effectiveness of a
            vaccine. To calculate it we divide the proportion of infected people
            in the vaccinated group (infected divided by infected plus
            uninfected) by the proportion of infected people in the unvaccinated
            group. Note that efficacy is zero if those proportions are the same
            and 100% if there are no infected members of the vaccinated group.
          </p>
        </div>
      </section>
      <section key="ungrouped">
        <div class="col-medium">
          <p>
            Here we see the two groups of vaccinated and unvaccinated with their
            relative numbers of uninfected and severely infected. The total
            efficacy of the vaccine is 67.5%, a number disturbingly low.
          </p>
          <p>
            Note that the majority of the unvaccinated are under 50 and that of
            those the large majority of severely infected are over 50.
          </p>
          <p>
            Scroll down to see what happens when we separate the these groups
            age, dealing with the over and under fifties separately. But first
            make sure you agree with the calculated efficacy.
          </p>
        </div>
      </section>
      <section key="grouped">
        <div class="col-medium">
          <p>
            By further separating the vaccinated and unvaccinated by age we get
            much more positive numbers for efficacy, demonstrating Simpson's
            paradox in action. By focusing on the two age defined subgroups we
            get a very different picture for the efficacy of the Covid vaccine.
          </p>
          <p>
            Scroll up and down between the two sections to see the transition
            and reassure yourself there's no sleight of hand. Try and get a feel
            for why the efficacies are so different between the aggregated
            groups and those separated by age.
          </p>
        </div>
      </section>

      <section class="section__end">
        <div class="col-medium">
          <h2>Conclusion</h2>
          <p>
            The previous section demonstrated that by separating our vaccinated
            and unvaccinated population by age we get a very different
            indication of the overall efficacy of the Covid vaccine. If policy
            makers were given the efficacy of the total population (67.5%) they
            may well conclude a need for a booster shot for the vulnerable
            groups, potentially diverting precious vaccine resources from the
            unvaccinated. However, if they were shown the efficacy by age (92.1%
            for the under 50s, 85.8% for the over 50s) it is unlikely a booster
            shot would be found necessary. In this instance the ability to grasp
            Simpson's paradox is not a cool statistical insight but could have
            life and death consequences in the real world.
          </p>
        </div>
      </section>
    </div>
  </Scroller>

  <!-- <Divider /> -->

  <!-- <Section>
  <h2>This is a wide media grid demo</h2>
  <p>
    Below is an example of a media grid where the column width is set to "wide"
    and the grid width is set to "narrow".
  </p>
</Section>

<Media
  col="wide"
  grid="narrow"
  caption="This is an optional caption for the above media."
>
  <div class="media">media</div>
  <div class="media">media</div>
  <div class="media">media</div>
  <div class="media">media</div>
</Media> -->

  <!-- <Section>
    <h2>Conclusion</h2>
    <p>
      The previous section demonstrated that by separating our vaccinated and
      unvaccinated population by age we get a very different indication of the
      overall efficacy of the Covid vaccine. If policy makers were given the
      efficacy of the total population (67.5%) they may well conclude a need for
      a booster shot for the vulnerable groups, potentially diverting precious
      vaccine resources from the unvaccinated. However, if they were shown the
      efficacy by age (92.1% for the under 50s, 85.8% for the over 50s) it is
      unlikely a booster shot would be found necessary. In this instance the
      ability to grasp Simpson's paradox is not a cool statistical insight but
      could have life and death consequences in the real world.
    </p>
  </Section>

  <ONSFooter /> -->
</div>

<style lang="scss">
  /* Styles specific to elements within the demo */
  .label-block {
    display: inline-block;
    text-align: right;
    width: 80px;
  }
  select {
    width: 210px;
  }
  .chart {
    // height: 100%;
    /* margin-top: 45px; */
    //height: 50vh;
    //height: 50vh; width: calc(100% - 5px);
  }
  /* The properties below make the media DIVs grey, for visual purposes in demo */
  .media {
    background-color: #f0f0f0;
    display: -webkit-box;
    display: -ms-flexbox;
    display: flex;
    -webkit-box-orient: vertical;
    -webkit-box-direction: normal;
    -ms-flex-flow: column;
    flex-flow: column;
    -webkit-box-pack: center;
    -ms-flex-pack: center;
    justify-content: center;
    text-align: center;
    color: #aaa;
  }
  .bg-white {
    background: white;
  }

  .hidden {
    //visibility: hidden;
    opacity: 0;
  }

  .image {
    transition: 1.5s ease all;
  }

  .image img {
    padding: 2em 0.5em;
  }

  .image,
  .chart {
    background: white;
  }

  section.section__end {
    padding: 0 0 10vh 0;
  }
</style>
