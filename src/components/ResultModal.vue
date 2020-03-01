<template>
  <div class="modal" v-bind:class="modalActivate">
    <div class="modal-background"></div>
    <div class="modal-card">
      <section class="modal-card-body">
        <article class="message is-primary">
          <div class="message-header">
            <p>Congratulations!!</p>
          </div>
        </article>

        <div class="columns is-mobile is-centered">
          <div class="column is-half">
            <div class="table-container">
              <table class="table is-fullwidth">
                <thead>
                  <tr>
                    <th>Key</th>
                    <th>Missed</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="(m, i) in groupByResult" v-bind:key="i">
                    <td>{{ m[0] }}</td>
                    <td>{{ m[1] }}</td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </section>
      <footer class="modal-card-foot">
        <div>
          <!-- <button class="button is-success">Save changes</button> -->
          <button class="button" @click="resetResult">Reset</button>
        </div>
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

<style scoped>
th,
td {
  text-align: center !important;
}

footer {
  flex-direction: column;
  justify-content: center;
}

.modal-background {
  background-color: rgba(3, 6, 79, 0.86) !important;
}
</style>