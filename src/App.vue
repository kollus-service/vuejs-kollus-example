<script>
export default {
  mounted() {
    const vgControllerScript = document.createElement("script");
    vgControllerScript.setAttribute(
      "src",
      "https://file.kollus.com/vgcontroller/vg-controller-client.latest.min.js"
    );
    document.head.appendChild(vgControllerScript);
    
    window.onload = () => {
      this.initVgController();
    }
  },
  methods: {
    // Video Gateway Controller Initializing
    initVgController() {
      console.log("initVgController Start");
      let controller = new VgControllerClient({
        target_window: document.querySelector('#kollus-player').contentWindow
      });

      // Ready Listener
      // 자세한 설명은 https://catenoid-support.atlassian.net/wiki/spaces/SUP/pages/3312250/V+G+Controller 를 참고하세요.
      controller.on('ready', function () {
        console.log('vg controller ready');
      });
    }
  },
}
</script>

<template>
  <main>
    <!-- 해당 위치에 LIVE 채널 URL 을 넣어주세요. -->
    <iframe id="kollus-player" src="https://v-live-kr.kollus.com/s?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJjbGllbnRfdXNlcl9pZCI6IuyehOuMgO2YhDEyMzAiLCJjbGllbnRfdXNlcl9uYW1lIjoi7J6E64yA7ZiEMTIzMCIsImV4cGlyZV90aW1lIjo0MTIzNDM4Njk5LCJwbGF5X2V4cHQiOjQxMjM0Mzg2OTksImxpdmVfbWVkaWFfY2hhbm5lbF9rZXkiOiJmb2dpanZwdG13N2R2ejJ2In0.XfqezmoyWr6uNj2kAHwJmBPhVSlS-EWPg0n8kzUg--s&custom_key=491abc8894f0200fd7a3c6dd6f28ac07654ee0487c15422aa9eed3e269c3a07d&autoplay" allow="autoplay;encrypted-media;fullscreen" width="1024" height="768" allowfullscreen="" webkitallowfullscreen="" mozallowfullscreen="" style="display: block;"></iframe>
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>