<!-- Pug case statements do not work for our hydration purposes -->
<template lang="pug">
  .schema-table
    table(v-bind:class="{hidden: showMobileTable}")
      thead
        tr
          th(v-for="(value, key) in schema[0]" v:key=key)
            span {{ key }}
      tbody
        tr(v-for="(value, key) in schema" v:key=key)
          td(v-for="(value, key) in value" v:key=key)
            div(v-if="key === 'property'")
              h3(:id="value") {{ value }}
                a(:href="'#' + value" aria-hidden="true" class="header-anchor") #
            div(v-else-if="key === 'type'")
              em {{ value }}
            div(v-else-if="key === 'example' || key === 'value'")
              code.land-cycler {{ value }}
            div(v-else)
              div.land-cycler {{ value }}

    div.mobile-doc-tables(v-bind:class="{hidden: !showMobileTable}")
      div.mobile-doc-table(v-for="(value, key) in schema" v:key=key v-bind:class="{hidden: !showMobileTable}")
        div.mobile-doc-table--row(v-for="(value, key) in value" v:key=key)
          div.mobile-doc-table--row-item(v-if="key === 'property'")
            div.mobile-doc-table--row-item-key
              h3(:id="value") {{ key }}
                a(:href="'#' + value" aria-hidden="true" class="header-anchor") #
            div.mobile-doc-table--row-item-value
              span {{ value }}
          div.mobile-doc-table--row-item(v-else-if="key === 'type'")
            div.mobile-doc-table--row-item-key
              span {{ key }}
            div.mobile-doc-table--row-item-value
              em {{ value }}
          div.mobile-doc-table--row-item(v-else-if="key === 'example' || key === 'value'")
            div.mobile-doc-table--row-item-key
              span {{ key }}
            div.mobile-doc-table--row-item-value
              code.land-cycler {{ value }}
          div.mobile-doc-table--row-item(v-else)
            div.mobile-doc-table--row-item-key
              span {{ key }}
            div.mobile-doc-table--row-item-value
              div.land-cycler {{ value }}

</template>

<script>
import Landcycle from '../modules/Landcycle';

export default {
  name: 'GenerateTable',
  data() {
    return {
      isMobileSize: 960,
      isMobile: false,
      schema: [],
    };
  },
  created() {
    this.schema = require(`../public/schemas/${
      this.$page.frontmatter.schema
    }.schema.json`);
  },
  mounted() {
    window.addEventListener('resize', this.checkIfMobile);
    this.checkIfMobile();
  },
  methods: {
    checkIfMobile() {
      this.isMobile = Math.max(window.innerWidth || 0) < this.isMobileSize;
      this.hydrate();
    },
    hydrate() {
      const hand = Array.from(this.$el.querySelectorAll('.land-cycler'));
      new Landcycle(hand);
    }
  },
  computed: {
    showMobileTable() {
      return this.isMobile;
    },
  },
};
</script>

<style lang="stylus" scoped>
h3 {
  font-size: 16px;
  padding: 0;
  margin: 0;
  font-weight: normal;
  position: relative;

  a {
    position: absolute;
    left: 0;
  }
}

.hidden {
  max-height: 0;
  padding: 0;
  border-width: 0;
  margin: 0;
  overflow: hidden;
}

table {
  &, * {
    box-sizing: border-box;
  }

  tr {
    width: 100%;
    // display: table-cell
  }

  th {
    padding: 10px 15px;
    text-align: left;
    text-transform: capitalize;
  }

  td {
    padding: 10px 15px;

    &:nth-of-type(2) {
      white-space: nowrap;
    }
  }

  thead, tbody {
    width: 100%;
  }

  tbody {
    tr:nth-of-type(odd) {
      background-color: $tableAltBgColor;
    }

    tr:nth-of-type(even) {
      background-color: transparent;
    }
  }
}

.mobile-doc-table {
  padding: 0;
  margin: 0;
  margin-top: 20px;
  margin-bottom: 30px;
  border: 1px solid $borderColor;
  box-sizing: border-box;

  * {
    font-size: 15px;
  }

  h3 {
    font-weight: bold;
  }

  &--row {
    &:last-of-type {
      .mobile-doc-table--row-item {
        &:last-of-type {
          border-bottom-width: 0;
        }
      }
    }

    &-item {
      width: 100%;
      display: inline-flex;
      border-bottom: 1px solid $borderColor;

      &-key {
        text-transform: capitalize;
        font-weight: bold;
        background-color: $tableAltBgColor;
        flex: 0 0 40%;
        display: flex;

        & > * {
          margin: 10px;
        }
      }

      &-value {
        flex: auto;
        padding: 10px;
        border-left: 1px solid $borderColor;
        overflow-wrap: anywhere;

        code {
          font-size: 14px;
        }
      }
    }
  }
}

code,
pre {
  &:empty {
    display: none;
  }
}
</style>
