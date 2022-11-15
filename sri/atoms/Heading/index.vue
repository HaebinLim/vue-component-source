<script>
import merge from 'lodash/merge';

export default {
  props: {
    rank: {
      type: Number,
      required: true,
      default: 1,
      validator(rank) {
        return rank >= 1 && rank <= 6;
      },
    },
    isHidden: {
      type: Boolean,
      required: false,
      default: false,
    },
    textContent: {
      type: String,
      require: false,
      default: ``,
    },
    html: {
      type: String,
      require: false,
      default: ``,
    },
  },
  render(createElement) {
    return createElement(
      `h${this.rank}`,
      merge(
        {
          class: [this.isHidden && this.$style['--is-hidden']],
        },
        !this.$slots.default && this.html
          ? {
              domProps: {
                innerHTML: this.html,
              },
            }
          : null,
      ),
      this.$slots.default || this.textContent,
    );
  },
};
</script>

<style lang="scss" module src="./index.scss"></style>
