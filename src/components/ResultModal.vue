<template>
  <div class="modal" v-bind:class="modalActivate">
    <div class="modal-background"></div>
    <div class="modal-card">
      <header class="modal-card-head">
        <p class="modal-card-title">お疲れさまでした！</p>
      </header>
      <section class="modal-card-body">
        <div class="table-container">
          <table class="table">
            <tbody>
              <tr v-for="(m, i) in groupByResult" v-bind:key="i">
                <td>{{ m[0] }}</td>
                <td>{{ m[1] }}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </section>
      <footer class="modal-card-foot">
        <button class="button is-success">Save changes</button>
        <button class="button" @click="resetResult">リセット</button>
      </footer>
    </div>
  </div>
</template>

<script>
export default {
  name: "ResultModal",
  props: {
    active: {
      type: Boolean,
      required: true,
      default: false
    },
    missTypes: {
      type: Array
    }
  },
  computed: {
    modalActivate() {
      return this.active ? "is-active" : "";
    },
    groupByResult() {
      return this.missTypes.reduce((m, v) => {
        if (m.has(v)) {
          m.set(v, m.get(v) + 1);
        } else {
          m.set(v, 1);
        }
        return m;
      }, new Map());
    }
  },
  methods: {
    resetResult() {
      this.$emit("resetEvent");
    }
  }
};
</script>