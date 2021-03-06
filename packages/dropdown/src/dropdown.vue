<script>
  import Clickoutside from 'wn-tech-ui/src/utils/clickoutside';
  import Emitter from 'wn-tech-ui/src/mixins/emitter';
  import ElButton from 'wn-tech-ui/packages/button';
  import ElButtonGroup from 'wn-tech-ui/packages/button-group';

  export default {
    name: 'ElDropdown',

    componentName: 'ElDropdown',

    mixins: [Emitter],

    directives: { Clickoutside },

    components: {
      ElButton,
      ElButtonGroup
    },

    props: {
      trigger: {
        type: String,
        default: 'hover'
      },
      menuAlign: {
        type: String,
        default: 'end'
      },
      type: String,
      size: String,
      splitButton: Boolean,
      hideOnClick: {
        type: Boolean,
        default: true
      },
      showTimeout: {
        type: Number,
        default: 250
      },
      hideTimeout: {
        type: Number,
        default: 150
      }
    },

    data() {
      return {
        timeout: null,
        visible: false,
        triggerElm: null
      };
    },

    mounted() {
      this.$on('menu-item-click', this.handleMenuItemClick);
      this.initEvent();
    },

    watch: {
      visible(val) {
        this.broadcast('ElDropdownMenu', 'visible', val);
        this.$emit('visible-change', val);
      }
    },

    methods: {
      show() {
        if (this.triggerElm.disabled) return;
        clearTimeout(this.timeout);
        this.timeout = setTimeout(() => {
          this.visible = true;
        }, this.showTimeout);
      },
      hide() {
        if (this.triggerElm.disabled) return;
        clearTimeout(this.timeout);
        this.timeout = setTimeout(() => {
          this.visible = false;
        }, this.hideTimeout);
      },
      handleClick() {
        if (this.triggerElm.disabled) return;
        this.visible = !this.visible;
      },
      initEvent() {
        let { trigger, show, hide, handleClick, splitButton } = this;
        this.triggerElm = splitButton
          ? this.$refs.trigger.$el
          : this.$slots.default[0].elm;

        if (trigger === 'hover') {
          this.triggerElm.addEventListener('mouseenter', show);
          this.triggerElm.addEventListener('mouseleave', hide);

          let dropdownElm = this.$slots.dropdown[0].elm;

          dropdownElm.addEventListener('mouseenter', show);
          dropdownElm.addEventListener('mouseleave', hide);
        } else if (trigger === 'click') {
          this.triggerElm.addEventListener('click', handleClick);
        }
      },
      handleMenuItemClick(command, instance) {
        if (this.hideOnClick) {
          this.visible = false;
        }
        this.$emit('command', command, instance);
      }
    },

    render(h) {
      let { hide, splitButton, type, size } = this;

      var handleClick = _ => {
        this.$emit('click');
      };

      let triggerElm = !splitButton
        ? this.$slots.default
        : (<el-button-group>
            <el-button type={type} size={size} nativeOn-click={handleClick}>
              {this.$slots.default}
            </el-button>
            <el-button ref="trigger" type={type} size={size} class="el-dropdown__caret-button">
              <i class="el-dropdown__icon el-icon-caret-bottom"></i>
            </el-button>
          </el-button-group>);

      return (
        <div class="el-dropdown" v-clickoutside={hide}>
          {triggerElm}
          {this.$slots.dropdown}
        </div>
      );
    }
  };
</script>
