<script>
export default {
  mounted() {
    const vgControllerScript = document.createElement("script");
    vgControllerScript.setAttribute(
      "src",
      "https://file.kollus.com/vgcontroller/vg-controller-client.latest.min.js"
    );
    document.head.appendChild(vgControllerScript);

    const kollusChattingSDKScript = document.createElement("script");
    kollusChattingSDKScript.setAttribute(
      "src",
      "https://file.kollus.com/kollusChatting/sdk/KOLLUS_CHATTING_SDK.latest.js"
    );
    document.head.appendChild(kollusChattingSDKScript);
    
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

      // SDK Object - 서버 연결/종료 Methods
      const initConnectionControllers = (sdk) => {
        sdk.startConnection();
      }

      // Ready Listener
      // 자세한 설명은 https://catenoid-support.atlassian.net/wiki/spaces/SUP/pages/3312250/V+G+Controller 를 참고하세요.
      controller.on('ready', function () {
        console.log('vg controller ready');
        console.log("KOLLUS_CHATTING_SDK", KOLLUS_CHATTING_SDK);
        const sdk = KOLLUS_CHATTING_SDK.getKollusSDK(controller);

        console.log("sdk", sdk);

        try {
          const config = sdk.getConfig();

          // SDK Object - Event 등록
          sdk.on('join', function(msg) {
              // Event Result - 최대 30개의 최근 메시지 표시
              if (msg.length > 0) {
                  for (var i=0; i < msg.length; i++) {
                      appendPrevChatMsg(msg[i]);
                  }
              }

              // Join 이후 받을 수 있는 동일한 Config 값들 (Join 이전부터 받을 수 있는 값들은 SDK Document 참조)
              showJoinInfo(config);
          });

          // SDK Object - Event 등록
          sdk.on('chat', function(msg) {
              appendChatMsg(msg, config.isAdmin(), config.getUserID(), config.blockFeatureEnabled(), userId => sdk.blockUserByAdmin(userId));
          });

          // SDK Object - Event 등록
          sdk.on('blocked', function(userId) {
              console.log("Blocked: " + userId);
          });

          // SDK Object - 서버 연결/종료 설정
          initConnectionControllers(sdk);
          
          // SDK Object - Event 등록
          sdk.on('connect_status', function(status, retryCount) {
              appendConnectStatus(status, retryCount);

              const connect_available = status === 'disconnected' || status === 'disconnected_exceed';
              const disconnect_available = !connect_available;

              configureConnectControllers(connect_available, disconnect_available);

              if (status === 'disconnected' || status === 'disconnected_exceed') {
                  configurePlayerController(true);
                  showJoinInfo(null);
                  updateStatus(null);
                  document.getElementById('prev_chat_msgs').innerHTML = '';
                  document.getElementById('chat_msgs').innerHTML = '';
              }
          });

          // SDK Object - Event 등록
          sdk.on('status', function(status) {
              updateStatus(status);
          });

          // SDK Object - Event 등록
          sdk.on('nickname_changed', function(nickname) {
              alert(`닉네임이 ${nickname} 으로 설정되었습니다.`);

              showJoinInfo(sdk.getConfig());
          });
      } catch(e) {
          alert(e);
      }
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