<template>
  <div class="row">
    <div>
      <input type="radio" id="host" name="joinAs" value="host" />
      <label>Host</label><br />
      <input type="radio" id="Audience" name="joinAs" value="audience" />
      <label>Audience</label><br />
      <button type="button" id="join" @click="this.join()">Join</button>
      <button type="button" id="stop">Leave</button>
      <!-- <button type="button" id="leave" @click="this.leave()">Leave</button> -->
      <div id="me"></div>
    </div>
  </div>
</template>

<script>
import AgoraRTC from "agora-rtc-sdk-ng";
export default {
  name: "HelloWorld",
  data() {
    return {
      options: {
        // Pass your App ID here.
        appId: "0a7016bdc6ce4b7684bfa83d3064b822",
        // Set the channel name.
        channel: "poc4",
        // Pass your temp token here.
        token:
          "007eJxTYOg89ted//RG1eNCRXPuvdv597sjw6bfggI7M4Ws1JesmxaowGCQaG5gaJaUkmyWnGqSZG5mYZKUlmhhnGJsYGaSZGFk9GLxseSGQEaG2VU8LIwMEAjiszAU5CebMDAAALmzIHQ=",
        // Set the user ID.
        uid: 0,
        // Set the user role
        role: "host",
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
    $.remotePlayerContainer = document.getElementById("me");
    // // Create an instance of the Agora Engine
    // this.agoraEngine = AgoraRTC.createClient({ mode: "live", codec: "vp8" });
    // // Dynamically create a container in the form of a DIV element to play the remote video track.
    // this.remotePlayerContainer = document.createElement("div");
    // // Dynamically create a container in the form of a DIV element to play the local video track.
    // this.localPlayerContainer = document.createElement("div");
    // // Specify the ID of the DIV container. You can use the uid of the local user.
    // $.localPlayerContainer.id = $.options.uid;
    // // Set the textContent property of the local video container to the local user id.
    // $.localPlayerContainer.textContent = "Voce esta ao vivo ";
    // // Set the local video container size.
    // $.localPlayerContainer.style.width = "640px";
    // $.localPlayerContainer.style.height = "480px";
    // $.localPlayerContainer.style.padding = "15px 5px 5px 5px";

    // // Set the remote video container size.
    // $.remotePlayerContainer.style.width = "640px";
    // $.remotePlayerContainer.style.height = "480px";
    // $.remotePlayerContainer.style.padding = "15px 5px 5px 5px";
    // $.teste("--------------- passou aqui ---------------");
    // // Listen for the "user-published" event to retrieve a AgoraRTCRemoteUser object.
    // $.agoraEngine.on("user-published", async (user, mediaType) => {
    //   // Subscribe to the remote user when the SDK triggers the "user-published" event.
    //   await $.agoraEngine.subscribe(user, mediaType);
    //   console.log("subscribe success");
    //   // Subscribe and play the remote video in the container If the remote user publishes a video track.
    //   if (mediaType == "video") {
    //     $.this("ta no if video");
    //     $.addVideoContainer(String(user.uid));
    //     // Retrieve the remote video track.
    //     $.channelParameters.remoteVideoTrack = user.videoTrack;
    //     // Retrieve the remote audio track.
    //     $.channelParameters.remoteAudioTrack = user.audioTrack;
    //     // Save the remote user id for reuse.
    //     $.channelParameters.remoteUid = user.uid.toString();
    //     // Specify the ID of the DIV container. You can use the uid of the remote user.
    //     $.remotePlayerContainer.id = user.uid.toString();
    //     $.channelParameters.remoteUid = user.uid.toString();
    //     $.remotePlayerContainer.textContent =
    //       "ao vivo pessoa: " + user.uid.toString();
    //     // Append the remote container to the page body.
    //     document.body.append($.remotePlayerContainer);
    //     if ($.options.role != "host") {
    //       // Play the remote video track.
    //       $.channelParameters.remoteVideoTrack.play($.remotePlayerContainer);
    //     }
    //   }
    //   // Subscribe and play the remote audio track If the remote user publishes the audio track only.
    //   if (mediaType == "audio") {
    //     // Get the RemoteAudioTrack object in the AgoraRTCRemoteUser object.
    //     $.channelParameters.remoteAudioTrack = user.audioTrack;
    //     // Play the remote audio track. No need to pass any DOM element.
    //     $.channelParameters.remoteAudioTrack.play();
    //   }
    //   // Listen for the "user-unpublished" event.
    //   $.agoraEngine.on("user-unpublished", (user) => {
    //     console.log(user.uid + "has left the channel");
    //   });
    // });
  },
  methods: {
    teste: function (texto) {
      console.log(texto);
    },
    addVideoContainer: function (uid) {
      let streamDiv = document.createElement("div"); // Create a new div for every stream
      streamDiv.id = uid; // Assigning id to div
      streamDiv.style.transform = "rotateY(180deg)"; // Takes care of lateral inversion (mirror image)
      this.remotePlayerContainer.appendChild(streamDiv); // Add new div to container
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
      $.teste(" passoou aqui ===== audiencia =======");
      // Save the selected role in a variable for reuse.
      $.options.role = "audience";
      if (
        $.channelParameters.localAudioTrack != null &&
        $.channelParameters.localVideoTrack != null
      ) {
        // Unpublish local tracks to set the user role as audience.
        await $.agoraEngine.unpublish([
          $.channelParameters.localAudioTrack,
          $.channelParameters.localVideoTrack,
        ]);
        // Stop playing the local video track
        $.channelParameters.localVideoTrack.stop();
        if ($.channelParameters.remoteVideoTrack != null) {
          // Play the remote video stream, if the remote user has joined the channel.
          // $.channelParameters.remoteVideoTrack.play($.remotePlayerContainer);
          $.channelParameters.remoteVideoTrack.play($.remotePlayerContainer);
        }
      }
      // Call the method to set the role as Audience.
      await $.agoraEngine.setClientRole($.options.role);
    },
    host: async function () {
      const $ = this;
      $.teste(" passoou aqui ===== host =======");

      // Save the selected role in a variable for reuse.
      $.options.role = "host";
      // Call the method to set the role as Host.
      await $.agoraEngine.setClientRole($.options.role);
      if ($.channelParameters.localVideoTrack != null) {
        // Publish the local audio and video track in the channel.
        await $.agoraEngine.publish([
          $.channelParameters.localAudioTrack,
          $.channelParameters.localVideoTrack,
        ]);
        // Stop playing the remote video.
        // $.channelParameters.remoteVideoTrack.stop();
        // $.channelParameters.remoteVideoTrack.play($.remotePlayerContainer);
        // Start playing the local video.
        $.channelParameters.localVideoTrack.play($.localPlayerContainer);
        $.channelParameters.localVideoTrack.play($.remotePlayerContainer);
      }
    },
    join: async function () {
      const $ = this;
      $.teste(" ====== entrou join =======");

      // if (d// Client Setup
      // Defines a client for RTC
      const client = AgoraRTC.createClient({ mode: "rtc", codec: "vp8" });
      // Get credentials from the form
      // let appId = document.getElementById("app-id").value;
      // let channelId = document.getElementById("channel").value;
      // let token = document.getElementById("token").value || null;

      let appId = "0a7016bdc6ce4b7684bfa83d3064b822";
      let channelId = "poc4";
      let token =
        "007eJxTYOg89ted//RG1eNCRXPuvdv597sjw6bfggI7M4Ws1JesmxaowGCQaG5gaJaUkmyWnGqSZG5mYZKUlmhhnGJsYGaSZGFk9GLxseSGQEaG2VU8LIwMEAjiszAU5CebMDAAALmzIHQ=";
      // Create local tracks
      const [localAudioTrack, localVideoTrack] =
        await AgoraRTC.createMicrophoneAndCameraTracks();
      // Initialize the stop button
      $.initStop(client, localAudioTrack, localVideoTrack);
      // Play the local track
      localVideoTrack.play("me");
      // Set up event listeners for remote users publishing or unpublishing tracks
      client.on("user-published", async (user, mediaType) => {
        await client.subscribe(user, mediaType); // subscribe when a user publishes
        if (mediaType === "video") {
          $.addVideoContainer(String(user.uid)); // uses helper method to add a container for the videoTrack
          user.videoTrack.play(String(user.uid));
        }
        if (mediaType === "audio") {
          user.audioTrack.play(); // audio does not need a DOM element
        }
      });
      client.on("user-unpublished", async (user, mediaType) => {
        if (mediaType === "video") {
          $.removeVideoDiv(user.uid); // removes the injected container
        }
      });
      // Join a channnel and retrieve the uid for local user
      // const _uid = await client.join(appId, channelId, token, null);
      await client.join(appId, channelId, token, null);
      await client.publish([localAudioTrack, localVideoTrack]);
    },
    initStop: function (client, localAudioTrack, localVideoTrack) {
      const stopBtn = document.getElementById("stop");
      stopBtn.disabled = false; // Enable the stop button
      stopBtn.onclick = null; // Remove any previous event listener
      stopBtn.onclick = function () {
        client.unpublish(); // stops sending audio & video to agora
        localVideoTrack.stop(); // stops video track and removes the player from DOM
        localVideoTrack.close(); // Releases the resource
        localAudioTrack.stop(); // stops audio track
        localAudioTrack.close(); // Releases the resource
        client.remoteUsers.forEach((user) => {
          if (user.hasVideo) {
            this.removeVideoDiv(user.uid); // Clean up DOM
          }
          client.unsubscribe(user); // unsubscribe from the user
        });
        client.removeAllListeners(); // Clean up the client object to avoid memory leaks
        stopBtn.disabled = true;
      };
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

<style scoped>
.ship {
  display: flex;
}
</style>
