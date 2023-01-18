/* eslint-disable $rule */
<template>
  <div class="row">
    <div>
      <input type="radio" id="host" name="joinAs" value="host" />
      <label>Host</label><br />
      <input type="radio" id="Audience" name="joinAs" value="audience" />
      <label>Audience</label><br />
      <button type="button" id="join" @click="this.join()">Join</button>
      <button type="button" id="leave" @click="this.leave()">Leave</button>
    </div>
  </div>
</template>

<script>
import AgoraRTC from "agora-rtc-sdk-ng";
export default {
  data() {
    return {
      options: {
        // Pass your App ID here.
        appId: "0a7016bdc6ce4b7684bfa83d3064b822",
        // Set the channel name.
        channel: "poc4",
        // Pass your temp token here.
        token:
          "007eJxTYPCbuY8tdM3cw1O68/Vi49ku3p41VXdL3T625W2NhbxLmtUVGAwSzQ0MzZJSks2SU02SzM0sTJLSEi2MU4wNzEySLIyM1CxOJDcEMjIsU2piZGSAQBCfhaEgP9mEgQEAEDwd8Q==",
        // Set the user ID.
        uid: 0,
        // Set the user role
        role: "",
      },
      channelParameters: {
        // A variable to hold a local audio track.
        localAudioTrack: null,
        // A variable to hold a local video track.
        localVideoTrack: null,
        // A variable to hold a remote audio track.
        remoteAudioTrack: null,
        // A variable to hold a remote video track.
        remoteVideoTrack: null,
        // A variable to hold the remote user id.s
        remoteUid: null,
      },
      agoraEngine: {},
      localPlayerContainer: {},
      remotePlayerContainer: {},
    };
  },
  async mounted() {
    const $ = this;

    // Create an instance of the Agora Engine
    this.agoraEngine = AgoraRTC.createClient({
      mode: "live",
      codec: "vp8",
    });
    // Dynamically create a container in the form of a DIV element to play the remote video track.
    this.remotePlayerContainer = document.createElement("div");
    // Dynamically create a container in the form of a DIV element to play the local video track.
    this.localPlayerContainer = document.createElement("div");
    // Specify the ID of the DIV container. You can use the uid of the local user.
    $.localPlayerContainer.id = $.options.uid;
    // Set the textContent property of the local video container to the local user id.
    $.localPlayerContainer.textContent = "Local user " + $.options.uid;
    // Set the local video container size.
    $.localPlayerContainer.style.width = "340px";
    $.localPlayerContainer.style.height = "280px";
    $.localPlayerContainer.style.padding = "15px 5px 5px 5px";
    // Set the remote video container size.
    $.remotePlayerContainer.style.width = "640px";
    $.remotePlayerContainer.style.height = "480px";
    $.remotePlayerContainer.style.padding = "15px 5px 5px 5px";
    $.teste("--------------- passou aqui 1 ---------------");
    // Listen f  or the "user-published" event to retrieve a AgoraRTCRemoteUser object.
    $.agoraEngine.on("user-published", async (user, mediaType) => {
      // Subscribe to the remote user when the SDK triggers the "user-published" event.
      $.teste("======= esta dentro ==============================");
      await $.agoraEngine.subscribe(user, mediaType);
      console.log("subscribe success");
      // Subscribe and play the remote video in the container If the remote user publishes a video track.

      if (mediaType == "video") {
        // Retrieve the remote video track.
        $.channelParameters.remoteVideoTrack = user.videoTrack;
        // Retrieve the remote audio track.
        $.channelParameters.remoteAudioTrack = user.audioTrack;
        // Save the remote user id for reuse.
        $.channelParameters.remoteUid = user.uid.toString();
        // Specify the ID of the DIV container. You can use the uid of the remote user.
        $.remotePlayerContainer.id = user.uid.toString();
        $.channelParameters.remoteUid = user.uid.toString();
        $.remotePlayerContainer.textContent =
          "Remote user " + user.uid.toString();
        // Append the remote container to the page body.
        document.body.append($.remotePlayerContainer);
        if ($.options.role != "host") {
          // Play the remote video track.
          $.channelParameters.remoteVideoTrack.play($.remotePlayerContainer);
        }
      }
      // Subscribe and play the remote audio track If the remote user publishes the audio track only.
      if (mediaType == "audio") {
        // Get the RemoteAudioTrack object in the AgoraRTCRemoteUser object.
        $.channelParameters.remoteAudioTrack = user.audioTrack;
        // Play the remote audio track. No need to pass any DOM element.
        $.channelParameters.remoteAudioTrack.play();
      }
      // Listen for the "user-unpublished" event.
      $.agoraEngine.on("user-unpublished", (user) => {
        console.log(user.uid + "has left the channel");
      });
    });
  },
  methods: {
    teste: function (texto) {
      console.log(texto);
    },

    removeVideoDiv: function (elementId) {
      console.log("Removing " + elementId + "Div");
      let Div = document.getElementById(elementId);
      if (Div) {
        Div.remove();
      }
    },
    audience: async function () {
      const $ = this;
      // Save the selected role in a variable for reuse.
      $.options.role = "audience";
      $.teste(" passoou aqui ===== audiencia =======");
      $.teste($.channelParameters.localVideoTrack);

      if (
        $.channelParameters.localAudioTrack != null &&
        $.channelParameters.localVideoTrack != null
      ) {
        $.teste(" passoou aqui 2 ===== audiencia =======");
        // Unpublish local tracks to set the user role as audience.
        await $.agoraEngine.unpublish([
          $.channelParameters.localAudioTrack,
          $.channelParameters.localVideoTrack,
        ]);
        // Stop playing the local video track
        // $.channelParameters.localVideoTrack.stop();
        if ($.channelParameters.remoteVideoTrack != null) {
          // Play the remote video stream, if the remote user has joined the channel.
          $.channelParameters.remoteVideoTrack.play($.remotePlayerContainer);
        }
      }
      $.teste(" passoou aqui 3 ===== audiencia =======");
      // Call the method to set the role as Audience.
      await $.agoraEngine.setClientRole($.options.role);
    },

    host: async function () {
      const $ = this;
      $.teste(" passoou aqui ===== host =======");
      $.teste($.channelParameters.localVideoTrack);
      // Save the selected role in a variable for reuse.
      $.options.role = "host";
      // Call the method to set the role as Host.
      await $.agoraEngine.setClientRole($.options.role);
      if ($.channelParameters.localVideoTrack != null) {
        $.teste(" localvideo track eh diferente null ");
        // Publish the local audio and video track in the channel.
        await $.agoraEngine.publish([
          $.channelParameters.localAudioTrack,
          $.channelParameters.localVideoTrack,
        ]);
        // Stop playing the remote video.
        // $.channelParameters.remoteVideoTrack.stop();
        // Start playing the local video.
        $.channelParameters.localVideoTrack.play($.localPlayerContainer);
        // $.channelParameters.localVideoTrack.play($.remotePlayerContainer);
      }
    },
    join: async function () {
      const $ = this;
      $.teste(" ====== entrou join =======");

      if (document.getElementById("Audience").checked) {
        $.options.role = "audience";
      }

      if (document.getElementById("host").checked) {
        // $.host();
        $.options.role = "host";
      }
      await $.agoraEngine.join(
        // Join a channel.
        $.options.appId,
        $.options.channel,
        $.options.token,
        $.options.uid
      );
      // Create a local audio track from the audio sampled by a microphone.
      $.channelParameters.localAudioTrack =
        await AgoraRTC.createMicrophoneAudioTrack();
      // Create a local video track from the video captured by a camera.
      $.channelParameters.localVideoTrack =
        await AgoraRTC.createCameraVideoTrack();
      // Append the local video container to the page body.
      $.teste(" ---- 1 --------");
      if (document.getElementById("Audience").checked) {
        $.audience();
      }

      $.teste(" ------ 2 --------");
      if (document.getElementById("host").checked) {
        $.host();
        // $.options.role = "host";
      }
      $.teste(" ------ 3 --------");
      document.body.append($.localPlayerContainer);
      $.teste(" ====== deu append na div =======");

      // Publish the local audio and video track if the user joins as a host.
      if ($.options.role == "host") {
        // Publish the local audio and video tracks in the channel.
        $.teste("entrou no host");
        $.teste($.channelParameters);

        await $.agoraEngine.publish([
          $.channelParameters.localAudioTrack,
          $.channelParameters.localVideoTrack,
        ]);
        $.teste(" ====== user eh host! =======");
        // Play the local video track.
        $.channelParameters.localVideoTrack.play($.localPlayerContainer);
        console.log("publish success!");
      }
    },
    leave: async function () {
      const $ = this;

      // Destroy the local audio and video tracks.
      $.channelParameters.localAudioTrack.close();
      $.channelParameters.localVideoTrack.close();
      // Remove the containers you created for the local video and remote video.
      $.removeVideoDiv($.remotePlayerContainer.id);
      $.removeVideoDiv($.localPlayerContainer.id);
      // Leave the channel
      await $.agoraEngine.leave();
      console.log("You left the channel");
      // Refresh the page for reuse
      window.location.reload();
    },
  },
};
</script>
