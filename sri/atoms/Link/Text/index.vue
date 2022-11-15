<script>
import merge from 'lodash/merge';
export default {
  props: {
    to: {
      type: [String, Object, null],
      required: false,
      default: null,
    },
    exact: {
      type: Boolean,
      required: false,
      default: false,
    },
    replace: {
      type: Boolean,
      required: false,
      default: false,
    },
    append: {
      type: Boolean,
      required: false,
      default: false,
    },
    tag: {
      type: String,
      requried: false,
      default: 'a',
    },
    href: {
      type: [String, null],
      requried: false,
      default: null,
    },
    target: {
      type: [String, null],
      required: false,
      default: null,
      validator(target) {
        if (target === ``) return false;
        if (target === null) return true;
        // 일단 _blank와 _self만 허용
        return [`_blank`, `_self`].includes(target);
      },
    },
    label: {
      type: String,
      required: false,
      default: ``,
    },
    theme: {
      type: String,
      required: false,
      default: `bright`,
      validator(theme) {
        return [`bright`, `dark`].includes(theme);
      },
    },
    appearance: {
      type: String,
      required: true,
      default: `basic`,
      validator(type) {
        return [`basic`, `grey`].includes(type);
      },
    },
    useUnderLine: {
      type: Boolean,
      required: false,
      default: false,
    },
    hasUnderLine: {
      type: Boolean,
      required: false,
      default: false,
    },
  },
  computed: {
    noOpener() {
      if (!this.target) return null;
      return [`_self`].includes(this.target) ? null : `noopener`;
    },
  },
  render(createElement) {
    const contentProps = {
      domProps: {
        innerHTML: this.label,
      },
    };

    const nuxtLinkProps = {
      props: {
        to: this.to,
        exact: this.exact,
        replace: this.replace,
        append: this.append,
        tag: this.tag,
      },
    };

    const anchorProps = {
      attrs: {
        href: this.href,
        target: this.target,
        rel: this.noOpener,
      },
    };

    const classes = {
      class: {
        [this.$style.link]: true,
        [this.$style[`link--${this.appearance}`]]: true,
        [this.$style[`link--${this.theme}`]]: this.theme === `dark`,
        [this.$style['link--underline']]: this.useUnderLine,
        [this.$style['link--lined']]: this.hasUnderLine,
      },
    };

    return createElement(
      this.to ? `nuxt-link` : `a`,
      merge(
        this.label ? contentProps : null,
        this.to ? nuxtLinkProps : anchorProps,
        classes,
      ),
      [this.$slots.default],
    );
  },
};
</script>

<style module lang="scss" src="./index.scss"></style>
