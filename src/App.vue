<script setup lang="ts">
import StateManagementDemo from "./components/StateManagementDemoLoader.vue";
import ThemeToggle from "./components/ThemeToggle.vue";
import AnotherDemo from "./components/AnotherDemo.vue";
import { Button } from "./components/ui/button";
import { GithubIcon } from "lucide-vue-next";
</script>

<template>
  <div class="fixed top-4 right-4 flex gap-2 z-50">
    <Button size="icon" as-child>
      <a
        href="https://github.com/nidexingg/just-fucking-use-vue"
        target="_blank"
        rel="noopener noreferrer"
        title="View the source code on GitHub"
      >
        <GithubIcon class="h-4 w-4" />
      </a>
    </Button>
    <ThemeToggle />
  </div>

  <main class="prose dark:prose-invert prose-lg mx-auto max-w-[80ch] py-16 px-4">
    <h1>
      JUST FUCKING USE VUE <br />
      <small class="text-xs font-bold font-sans">
        (OR ANY FRAMEWORK, YOU PREHISTORIC FUCK)
      </small>
    </h1>

    <ul class="sr-only">
      <li>
        <a href="#start">Content</a>
      </li>
      <li>
        <a href="#wtf">What the fuck is this about?</a>
      </li>
      <li>
        <a href="#why">
          Why the fuck would I ditch my &quot;pure HTML&quot; Nirvana for this
          JavaScript sorcery?
        </a>
        <ul>
          <li>
            <a href="#components">
              Components, motherfucker! Do you speak it?
            </a>
          </li>
          <li>
            <a href="#ui">
              Smarter UI updates, so you don&apos;t have to be a fucking
              DOM-manipulating peasant.
            </a>
          </li>
          <li>
            <a href="#accessibility">
              Accessibility at scale? Good luck with your &quot;semantic&quot;
              div soup and prayers, asshole.
            </a>
          </li>
          <li>
            <a href="#developer-experience">
              Developer experience (DX) actually fucking matters, unless you
              enjoy pain.
            </a>
          </li>
          <li>
            <a href="#performance">
              Performance isn&apos;t just about initial load, it&apos;s about
              whether the thing still feels fast once people actually start
              using the bastard.
            </a>
          </li>
          <li>
            <a href="#why-vue-specifically">
              Why Vue specifically, and not raw HTML theater or framework fanboy
              wars?
            </a>
          </li>
        </ul>
      </li>
      <li>
        <a href="#bloat">
          But what about the fucking bloat? The JavaScript overload? My users
          are on dial-up in Antarctica!
        </a>
      </li>
      <li>
        <a href="#when">
          When should I just fucking use Vue (or a similar framework), you slow
          learner?
        </a>
      </li>
      <li>
        <a href="#vue-power-demo">
          Show me the actual Vue magic, not just your angry TED talk.
        </a>
      </li>
      <li>
        <a href="#vue-impossible-demo">
          Give me advanced shit that melts vanilla brains.
        </a>
      </li>
      <li>
        <a href="#problem">
          The real fucking problem isn&apos;t Vue. It&apos;s you, you misguided
          fool.
        </a>
      </li>
      <li>
        <a href="#footer">Footer</a>
      </li>
    </ul>

    <p id="start">
      Listen up, I totally get it. You might already saw that other site yapping
      <mark>&quot;HTML is king. Ohh!!! HTML assfucks me...&quot;</mark> like it just discovered the &lt;strong&gt; tag
      yesterday, mumbling &quot;keep it simple&quot; while it gently
      masturbates to a 10kb waterfall chart.
      <span class="geocities">
        And of course: &quot;My GeoCities page from &apos;98 still loads faster
        than your SPA.&quot;
      </span>
      That&apos;s not a flex, grandpa, that&apos;s a museum exhibit.
    </p>
    <p>
      Yes, raw HTML is fast to load and hard to break for simple content. No
      one with a functioning brain is disputing that. But the web grew up while
      you were busy polishing your
      &quot;view source&quot; purity ring: people run full-blown applications in
      the browser now — dashboards, editors, storefronts, multiplayer tools,
      internal systems — with real state and real logic, not just three
      paragraphs and a mailto link.
    </p>

    <h2 id="wtf">SO, WHAT THE FUCK IS THIS ABOUT?</h2>
    <p>
      This is about acknowledging that sometimes,
      <mark>
        complexity is not a choice, it's a fucking requirement. 
        And once you&apos;re building an actual application, client-side complexity
        isn&apos;t a cute preference, it&apos;s baked into the job.
      </mark>
      You can&apos;t just shout &quot;HTML!&quot; at the problem and hope your
      state, interactions, and data flows politely disappear.
    </p>
    <p>
      The only real choice is where that complexity lives: inside a framework
      that encodes sane patterns, or smeared chaotically across a mess of
      event handlers, global variables, and ad‑hoc hacks that future-you will
      stare at like a forensic pathologist at a crime scene.
    </p>

    <h2 id="why">
      WHY THE FUCK WOULD I DITCH MY &quot;PURE HTML&quot; NIRVANA FOR THIS
      JAVASCRIPT SORCERY?
    </h2>
    <p>
      Because you&apos;re not shipping a laminated menu, you clown. You want
      filtering, search, sorting, nested forms, auth, user-specific views,
      live‑updating data, and navigation that doesn&apos;t feel like clicking
      through an archive from the dial‑up era.
    </p>
    <p>
      Vue, React, Svelte, Solid, Angular — pick your cult. The brand doesn&apos;t
      matter nearly as much as using something designed for building big,
      interactive interfaces instead of trying to duct-tape a full SPA onto
      &quot;just some HTML and vibes&quot;.
    </p>
    <h3 id="why-vue-specifically">
      WHY VUE SPECIFICALLY? BECAUSE YOU WANT POWER WITHOUT NEEDLESS BRAIN
      DAMAGE.
    </h3>
    <p>
      Static HTML is amazing for documents and simple pages. The moment your
      UI starts behaving like an application, though, static markup alone turns
      into a part-time job of manually wiring events, syncing state, and praying
      you didn&apos;t forget one cursed DOM update in a corner case.
    </p>
    <p>
      Vue gives you the middle path: strong structure, reactivity that makes
      sense, and single-file components that keep template, logic, and style in
      one sane place instead of scattering your thoughts across seventeen files
      and a stress headache.
    </p>
    <p>
      Compared to heavier frameworks, Vue usually asks for less ceremony to get
      moving. Compared to ultra-minimal libs, Vue gives you more batteries out
      of the box so your team isn&apos;t rebuilding routing, patterns, and project
      conventions from scratch every sprint like amnesiac raccoons.
    </p>
    <p>
      Translation: if static HTML is too weak for the app you&apos;re building, and
      framework dogma is wasting your oxygen, Vue is often the practical answer.
      Not because it&apos;s holy, but because it lets people ship real features
      without writing front-end fanfiction.
    </p>

    <h3>
      HTML&apos;S &quot;INTERACTIVITY&quot; IS FOR PARTY TRICKS, NOT POWER
      TOOLS, YOU DELUSIONAL TWIT.
    </h3>
    <p>
      Yes, HTML gives you forms, <code>&lt;details&gt;</code>,
      <code>&lt;dialog&gt;</code>, and friends. Great. For small, linear flows,
      that&apos;s fantastic and you should absolutely use them.
    </p>
    <p>
      But don&apos;t pretend that a couple of built‑in tags magically replace
      the need for structured logic when you&apos;re dealing with conditional
      UIs, complex validation, partial saves, optimistic updates, and multiple
      parts of the screen reacting to one change. That&apos;s not being
      &quot;minimalist&quot;, that&apos;s being allergic to architecture.
    </p>
    <p>
      HTML is the foundation. It&apos;s not the whole damn building. You can
      live in a foundation if you like sleeping in the mud.
    </p>

    <h3 id="state-management">STATE MANAGEMENT, YOU MALFUNCTIONING PRIMATE.</h3>
    <p>
      Flipping a single boolean for a &quot;like&quot; button is toddler-level
      work. The real fun starts when auth status, filters, pagination, unsaved
      edits, notifications, and server data all collide, and every change needs
      to reflect across multiple pieces of UI without silently corrupting
      reality.
    </p>
    <p>
      Without a structured approach, your client-side state turns into
      grease‑coated spaghetti: functions calling functions calling timers
      calling callbacks, all with slightly different ideas about what &quot;the
      current state&quot; is supposed to be.
    </p>
    <StateManagementDemo />
    <p>
      Frameworks don&apos;t remove the complexity, but they at least give you
      predictable places to put it. Reactive state, stores, and patterns for
      sharing data across components are not &quot;overengineering&quot;, they&apos;re
      you admitting you&apos;re not going to keep the entire app in your head
      like some bug-ridden savant.
    </p>


    <h3 id="vue-power-demo">
      ALRIGHT, WANT MORE INSULTS? NOW DO THIS WITH HTML, YOU FUCK
    </h3>
    <p>
      This one stacks optimistic updates, rollback on random server failure,
      mutation logs, time-travel undo/redo, derived KPIs, and persistence.
      Exactly the kind of behavior that becomes a maintenance horror show when
      you build it as scattered event listeners and random globals.
    </p>
    <AnotherDemo />

    <h3 id="components">COMPONENTS, MOTHERFUCKER. LEARN THE WORD.</h3>
    <p>
      That nav bar you copy‑pasted into twelve files? Those three different
      modals with almost‑but‑not‑quite‑the‑same markup? That button that behaves
      slightly differently on every page because you kept tweaking it locally?
      That&apos;s what life without real components looks like.
    </p>
    <p>
      Vue makes you wrap markup, style, and behavior into actual reusable
      pieces, with clear inputs and outputs. It&apos;s boring. It&apos;s
      disciplined. It&apos;s exactly what you need if you don&apos;t want your
      front-end to feel like someone built it drunk over three years on random
      weekends.
    </p>
    <p>
      You can either have &quot;just HTML&quot; and endless copy‑paste, or you
      can have components and fix things once. Pick one.
    </p>

    <h3 id="ui">
      SMARTER UI UPDATES, SO YOU DON&apos;T HAVE TO POKE THE DOM LIKE A CAVE
      PERSON.
    </h3>
    <p>
      Manually deciding which little DOM fragment to update every time some bit
      of state changes is fine when your page is the complexity level of a
      toaster. But as soon as things interact in non-trivial ways, you&apos;re
      one missed update away from phantom bugs and ghost UIs.
    </p>
    <p>
      Frameworks give you a declarative model: you say &quot;here&apos;s what
      the UI looks like when the data is X&quot;, and the runtime sorts out the
      minimal changes. You&apos;re trading busywork and brittle manual patching
      for a system that doesn&apos;t forget that one text node in the sidebar.
    </p>
    <p>
      Or keep sprinkling <code>innerText = ...</code> everywhere like seasoning
      until nothing is synced and everything is cursed. Your call.
    </p>

    <h3 id="accessibility">
      ACCESSIBILITY AT SCALE? YOUR RANDOM DIVS AND GOOD INTENTIONS AREN&apos;T
      ENOUGH, ASSHOLE.
    </h3>
    <p>
      &quot;Just use semantic HTML&quot; is the right answer for static
      documents and simple interfaces. It&apos;s the minimum bar, and if you
      don&apos;t clear it, you&apos;re already failing.
    </p>
    <p>
      But the moment you start inventing custom widgets — combo boxes, complex
      dialogs, tree views, typeahead search, nested menus — you&apos;re in a
      world where ARIA roles, keyboard handling, and focus management have to be
      correct every single time, not just when you remember.
    </p>
    <p>
      A well-built component can encapsulate all that pain and let you reuse it
      without rewriting accessibility logic from scratch like a goldfish with a
      text editor. That&apos;s not theoretical; that&apos;s how grown-up UI
      libraries exist at all.
    </p>

    <h3 id="developer-experience">
      YOUR &quot;SIMPLE&quot; STACK HIDES A THROBBING BALL OF MANUAL WORK YOU
      REFUSE TO ADMIT EXISTS.
    </h3>
    <p>
      No framework means no router, no consistent state layer, no components, no
      devtools, no shared mental model, and no community-tested patterns — unless
      you rebuild them yourself, piece by janky piece, in the name of
      &quot;keeping it lean&quot;.
    </p>
    <p>
      Sometimes that&apos;s justified. Often it just means you traded
      framework complexity for custom complexity that only exists in your
      head and your half-documented codebase, and now every new teammate has to
      reverse‑engineer your personal religion before they can fix a bug.
    </p>
    <p>
      You&apos;re not escaping complexity; you&apos;re just hiding it under the
      floorboards and hoping it doesn&apos;t start chewing through the wiring.
    </p>

    <h3 id="global-js-vars">
      STILL RIDING HTML&apos;S AUTO-GLOBALS? CONGRATS, YOU&apos;RE CODING LIKE
      IT&apos;S 1999.
    </h3>
    <p>
      Browsers exposing <code>id</code>-named elements as globals is a
      historical quirk, not a design pattern. Leaning on that in 2026 is like
      proudly announcing you debug with <code>alert()</code> and vibes.
    </p>
    <p>
      Global namespace pollution, collisions, and invisible implicit wiring are
      exactly the kind of thing that make large frontends impossible to reason
      about. Explicit imports, scoped modules, and components exist to stop
      that madness.
    </p>
    <p>
      Frameworks shove you away from &quot;everything on window&quot; and toward
      code that doesn&apos;t explode the second you add another feature. That&apos;s
      not &quot;fancy&quot;; it&apos;s just not terrible.
    </p>

    <h3 id="developer-experience">
      DEVELOPER EXPERIENCE ACTUALLY FUCKING MATTERS, UNLESS YOUR HOBBY IS
      SUFFERING.
    </h3>
    <p>
      Hot reload, TypeScript integration, component inspectors, router devtools,
      ecosystem libraries, form helpers — none of that is essential for a three
      page site, but it absolutely pays off once you have a non-trivial
      codebase and more than one human working on it.
    </p>
    <p>
      The whole point is shorter feedback loops and fewer stupid, preventable
      mistakes. You can sneer at that and hand‑edit everything if you want, but
      don&apos;t pretend the rest of us are weak for liking tools that actually
      get out of the way.
    </p>
    <p>
      Frameworks like Vue pull a lot of that together so you&apos;re not stuck
      gluing random libraries and scripts into a Frankenstack that only you can
      operate.
    </p>

    <h3 id="performance">
      PERFORMANCE ISN&apos;T JUST &quot;FIRST BYTE FAST&quot;, IT&apos;S &quot;DOES
      THE DAMN THING STAY FAST WHEN YOU USE IT&quot;, YOU MYOPIC CLOWN.
    </h3>
    <p>
      Yes, HTML with almost no JS will usually win a synthetic benchmark on
      first load. If your product is a static page, that&apos;s the end of the
      discussion — ship HTML and go outside.
    </p>
    <p>
      But interactive apps live and die on how snappy they feel after that first
      load: instant navigation, smooth interactions, partial updates without
      full reloads, responsive UI under real usage. That&apos;s where SPA
      patterns, code-splitting, SSR/SSG, and client-side routing actually earn
      their keep.
    </p>
    <p>
      You can still screw it up with bloated bundles and lazy coding, obviously.
      The framework is a tool, not a spell. But pretending &quot;no framework =
      automatically faster&quot; once you&apos;re building an application is
      cargo cult performance.
    </p>

    <h2 id="bloat">
      BUT WHAT ABOUT THE FUCKING BLOAT? THE JAVASCRIPT FLOOD? MY USERS ARE ON A
      TOASTER.
    </h2>
    <p>
      Of course shipping a framework costs more bytes than a bare HTML file.
      That&apos;s like complaining your toolbox weighs more than a single
      screwdriver. The question is whether the job justifies carrying the box.
    </p>
    <p>
      For tiny, mostly static sites — marketing pages, personal blogs, simple
      docs — you absolutely do not need Vue. HTML, CSS, maybe a sprinkling of
      vanilla JS and you&apos;re golden. Anything else is ego cosplay.
    </p>
    <p>
      But once you&apos;re in app territory, you&apos;re shipping JavaScript
      regardless. A framework doesn&apos;t create that reality; it organizes it.
      Modern tooling lets you split bundles, lazy-load routes, and only ship
      what&apos;s needed per view, instead of dunking the whole codebase on
      every request.
    </p>

    <h2 id="when">
      SO, WHEN SHOULD I JUST FUCKING USE VUE (OR A SIMILAR FRAMEWORK), YOU SLOW
      LEARNER?
    </h2>
    <ul>
      <li>
        When you have <strong>non-trivial client-side state</strong> that many
        parts of the UI depend on.
      </li>
      <li>
        When you want <strong>reusable components</strong> instead of a
        copy‑paste carnival.
      </li>
      <li>
        When you&apos;re building a <strong>SPA or app-like experience</strong>,
        not just a fancy PDF with links.
      </li>
      <li>
        When you&apos;re on a <strong>team</strong> and everyone needs to share
        a mental model instead of decoding your personal chaos.
      </li>
      <li>
        When the <strong>interaction and state complexity</strong> would turn
        vanilla JS into &quot;that one person&quot; nobody wants to touch.
      </li>
      <li>
        When you actually want a <strong>real ecosystem</strong> for routing,
        forms, testing, and data, instead of gluing random scripts together.
      </li>
      <li>
        When <strong>long-term maintainability and speed of building</strong>
        matter more than winning purity arguments on Reddit.
      </li>
    </ul>

    <h2 id="problem">
      THE REAL FUCKING PROBLEM ISN&apos;T VUE. IT&apos;S YOUR TOOL CHOICE
      BRAINWORM.
    </h2>
    <p>
      Using Vue for a single static page is dumb. Refusing Vue for a huge,
      interactive, stateful app is also dumb. In both cases, the issue isn&apos;t
      the tech, it&apos;s your inability to match the tool to the problem.
    </p>
    <p>
      Frameworks are not magic. HTML is not holy. They each solve different
      classes of problems well. Treating either as a religion is how you end up
      with bloated toy apps or stone-age UX, depending on which hill you choose
      to die on.
    </p>
    <p>
      For your next complex, interactive, data-heavy web thing:
      <mark>JUST FUCKING USE VUE.</mark>
      For a small, mostly static site: just fucking use HTML and stop overdoing
      it. That&apos;s it. That&apos;s the whole wisdom. The rest is you
      learning to tell those two categories apart.
    </p>
    <p>
      Now shut the tab, pick the correct tool, and build something that doesn&apos;t
      make future-you want to walk into the sea.
    </p>

    <h2 id="footer" class="sr-only">
      FOOTER
    </h2>
    <p>
      <strong>Ported to Vue and rewritten by:</strong>
      <br />
      <span>
        Chandara H. Wei (
        <a
          href="https://go.nidexingg.com/gh"
          target="_blank"
          rel="noopener noreferrer"
          title="View Chandara's GitHub profile"
        >
          GitHub
        </a>
        /
        <a
          href="https://go.nidexingg.com/x"
          target="_blank"
          rel="noopener noreferrer"
          title="View Chandara's X profile"
        >
          Twitter
        </a>
        /
        <a
          href="https://go.nidexingg.com/bsky"
          target="_blank"
          rel="noopener noreferrer"
          title="View Chandara's Bluesky profile"
        >
          Bluesky
        </a>
        )
      </span>
    </p>
    <p>
      <strong>Inspired and ported from:</strong>
      <br />
      <span>
        Omercan (
        <a
          href="https://github.com/obsfx"
          target="_blank"
          rel="noopener noreferrer"
          title="View Omercan's GitHub profile"
        >
          GitHub
        </a>
        /
        <a
          href="https://x.com/obsfx"
          target="_blank"
          rel="noopener noreferrer"
          title="View Omercan's X profile"
        >
          Twitter
        </a>
        /
        <a
          href="https://bsky.app/profile/omercan.io"
          target="_blank"
          rel="noopener noreferrer"
          title="View Omercan's Bluesky profile"
        >
          Bluesky
        </a>
        )
      </span>
      <br />
      <span>
        Burak (
        <a
          href="https://github.com/burakcan"
          target="_blank"
          rel="noopener noreferrer"
          title="View Burak's GitHub profile"
        >
          GitHub
        </a>
        /
        <a
          href="https://x.com/neoberg"
          target="_blank"
          rel="noopener noreferrer"
          title="View Burak's X profile"
        >
          Twitter
        </a>
        /
        <a
          href="https://bsky.app/profile/burakcan.me"
          target="_blank"
          rel="noopener noreferrer"
          title="View Burak's Bluesky profile"
        >
          Bluesky
        </a>
        )
      </span>
    </p>
    <p>
      Inspired by
      <a
        href="https://justfuckingusehtml.com/"
        target="_blank"
        rel="noopener noreferrer"
        title="View justfuckingusehtml.com"
      >
        justfuckingusehtml.com
      </a>
    </p>
    <p>
      <strong>
        <a
          href="https://github.com/nidexingg/just-fucking-use-vue"
          target="_blank"
          rel="noopener noreferrer"
          title="View the source code on GitHub"
        >
          View the source code on GitHub
        </a>
      </strong>
    </p>
  </main>
</template>