<template>
  <div>
      <slot
        name="toolbar"
        :today="today"
        :back="() => addPeriod(-1)"
        :forward="() => addPeriod(1)"
        :daily="() => switchview(1)"
        :weekly="() => switchview(7)"
        :monthly="() => switchview(31)"
        :title="title"
        :type="type"
      >
      </slot>
</div>
</template>

<script>

  import moment from 'moment'

  export default {
    name: 'Calendar',
    components: {},
    props: ['currentday','viewdays'],
    mounted() {
    },
    created() {
    },
    data: () => ({
      type: 31,
      todaydate: moment().format('l'),
    }),
    methods: {
      addPeriod(value) {
        if ( this.viewdays ===7 ) {
          if (value > 0 || parseInt(moment(this.currentday).format('e')) === 0) {
            this.$emit('goTo', moment(this.currentday).add(value, 'week').startOf('week').format('l'))
          } else {
            this.$emit('goTo', moment(this.currentday).startOf('week').format('l'))
          }
        }
        else if ( this.viewdays === 1 ) {
          this.$emit('goTo', moment(this.currentday).add(value, 'day').format('l'));
        }
        else {
          if (value > 0 || parseInt(moment(this.currentday).format('D')) === 1) {
            this.$emit('goTo', moment(this.currentday).add(value, 'month').startOf('month').format('l'));
          } else {
            this.$emit('goTo', moment(this.currentday).startOf('month').format('l'))
          }
        }
      },
      today() {
        this.$emit('goTo', this.todaydate)
      },
      switchview(value) {
        this.type=value
        this.$emit('changeView', value)
      }
    },
    watch: {},
    computed: {
      monthtitle() {
        return parseInt(moment(this.currentday).format('D')) === 1 ?
        moment(this.currentday).format('MMMM YYYY') :
        moment(this.currentday)
          .format('dddd DD MMM') + ' - ' + moment(this.currentday).add(this.viewdays-1,'days')
            .format('dddd DD MMM');
      },
      day() {
        return parseInt(moment(this.currentday).format('D'))
      },
      dailytitle() {
        return moment(this.currentday)
          .format('dddd D MMMM');
      },
      title() {
        return this.viewdays ===1 ? this.dailytitle:this.monthtitle
      },
      hasToolbarSlot() {
        console.log(!!this.$slots.toolbar)
        return !!this.$slots.toolbar
      }
    }
  }

</script>
<style scoped>

</style>
