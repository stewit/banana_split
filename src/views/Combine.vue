<template>
  <div>
    <div class="card measure" :transparent="!recoveredSecret">
      <h2 class="card-title">
        Combine shards
        <span v-if="title">
          for <em v-if="title"> {{ title }}</em>
        </span>
      </h2>
      <div v-if="needMoreShards">
        Scan QR code... <br>
        <qrcode-stream @decode="onDecode" />
        <hr style="width:75%;margin-top:10px;margin-bottom:10px">
        ... or enter shard json from printout below!
        <textarea
          id="shard_json_str"
          v-model="shard_json_str"          
          placeholder="Enter shard json manually. Use this only if QR code scanning does not work."
          style="margin-bottom:5px"
          spellcheck="false"
        />
        <button id="add_shard" class="button-card" style="margin-bottom:10px" @click="addshardjson">
          Add shard json
        </button>        
      </div>
      <div v-else>
        <p>
          <label>1. Secret Phrase</label>
          <input
            id="passphrase"
            v-model="passphrase"
            type="text"
            placeholder="type your passphrase"
            autofocus
            @keyup.enter="reconstruct"
          />
        </p>
        <p>
          <label>2. Secret</label>
          <textarea
            v-if="recoveredSecret"
            id="recoveredSecret"
            v-model="recoveredSecret"
            spellcheck="false"
            readonly
          />
          <textarea v-else readonly disabled />
        </p>
        <div v-if="!recoveredSecret">
          <button id="reconstructBtn" class="button-card" @click="reconstruct">
            Reconstruct Secret
          </button>
        </div>
      </div>
    </div>

    <div class="card flex" transparent="true" :framed="recoveredSecret && true">
      <qriously
        v-for="code in qrCodes"
        :key="code"
        class="card-qr"
        :value="code"
        :size="200"
      />
      <qriously
        v-for="n in remainingCodes"
        :key="n"
        class="remaining card-qr"
        :value="PLACEHOLDER_QR_DATA"
        :size="200"
      />
    </div>
  </div>
</template>

<script lang="ts">
import crypto, { Shard } from "../util/crypto";
import Vue from "vue";

type CombineData = {
  title: string;
  nonce: string;
  shard_json_str: string;
  shards: Shard[];
  qrCodes: Set<string>;
  requiredShards?: number;
  passphrase: string;
  recoveredSecret?: string;
  PLACEHOLDER_QR_DATA: string;
};

export default Vue.extend({
  name: "Combine",
  data(): CombineData {
    return {
      title: "",
      nonce: "",
      shard_json_str: "",
      shards: [],
      qrCodes: new Set(),
      requiredShards: undefined,
      passphrase: "",
      recoveredSecret: undefined,
      PLACEHOLDER_QR_DATA: ""
    };
  },
  computed: {
    needMoreShards(): boolean {
      return !this.requiredShards || this.shards.length < this.requiredShards;
    },
    remainingCodes(): number {
      if (!this.requiredShards) {
        return 0;
      } else {
        return this.requiredShards - this.shards.length;
      }
    }
  },
  mounted: function() {
    this.$eventHub.$emit("foldGeneralInfo");
    this.$eventHub.$emit("clearAlerts");
  },
  methods: {
    onDecode: function(result: string): string | void {
      this.$eventHub.$emit("clearAlerts");
      if (result === "") {
        return;
      }
      if (this.qrCodes.has(result)) {
        this.$eventHub.$emit("showWarn", "Shard already seen");
        return;
      }
      let parsed;
      try {
        parsed = crypto.parse(result);
      } catch (error) {
        this.$eventHub.$emit("showError", error);
        return;
      }

      if (this.title && this.title !== parsed.title) {
        this.$eventHub.$emit("showError", "title mismatch!");
        return;
      } else {
        this.title = parsed.title;
      }
      if (this.nonce && this.nonce !== parsed.nonce) {
        this.$eventHub.$emit("showError", "nonce mismatch!");
        return;
      } else {
        this.nonce = parsed.nonce;
      }
      if (
        this.requiredShards &&
        this.requiredShards !== parsed.requiredShards
      ) {
        this.$eventHub.$emit("showError", "requiredShards mismatch");
        return;
      } else {
        this.requiredShards = parsed.requiredShards;
      }
      this.qrCodes.add(result);
      this.shards.push(parsed);
    },
    reconstruct: function(): void {
      if (!this.passphrase) {
        return;
      }
      this.passphrase = this.passphrase
        .split(" ")
        .filter(el => el)
        .join("-");
      const shards = Array.from(this.shards);
      try {
        this.recoveredSecret = crypto.reconstruct(shards, this.passphrase);
      } catch (error) {
        this.$eventHub.$emit("showError", error);
      }
    },

    addshardjson: function(): string | void {

      let result;
      result = this.shard_json_str;

      // remove linebreaks, then decode
      let decode_result;
      decode_result = this.onDecode(result.replace(/(\r\n|\n|\r)/gm, ""));

      this.shard_json_str = "";

      return decode_result;

      
    }
  }
});
</script>

<style>
.remaining {
  filter: blur(5px);
  opacity: 0.5;
}
/* Flip video to make it easier to use */
.qrcode-stream {
  transform: scaleX(-1);
  border-radius: 8px;
  overflow: hidden;
}
</style>
