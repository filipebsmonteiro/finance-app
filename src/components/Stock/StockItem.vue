<script setup>
import formaters from "src/boot/providers/formatters";

const props = defineProps({
  item: Object,
  sections: {
    type: Array,
    default(rawProps) {
      return [
        {
          key: `logo`,
          side: true,
          img: {
            src: (item) => item.logo,
          },
        },
        {
          key: `code`,
          side: true,
        },
        {
          key: `name`,
          class: `col`,
          side: true,
          // hide: this.$q.screen.lt.md,
        },
        {
          key: `result`,
          class: `col`,
          label: {
            class: (item) => ({
              "text-caption": true,
              "text-positive": item.result > 0,
              "text-negative": item.result < 0,
              "text-grey": item.result === 0,
            }),
            format: formaters.money,
          },
        },
        {
          key: `quantity`,
          class: `col`,
          // hide: this.$q.screen.lt.md,
        },
        {
          key: `quotation`,
          class: `col`,
          badge: {
            color: (item) =>
              item.quotation.change > 0
                ? "positive"
                : item.quotation.change < 0
                ? "negative"
                : "grey",
            icon: (item) =>
              item.quotation.change > 0
                ? "la la-arrow-up"
                : item.quotation.change < 0
                ? "la la-arrow-down"
                : "la la-minus",
          },
          label: {
            class: (item) => ({
              "text-caption": true,
              "text-positive": item.result > 0,
              "text-negative": item.result < 0,
              "text-grey": item.result === 0,
            }),
            format: (quotation) =>
              `${(parseFloat(quotation.change) || 0).toFixed(2)}%`,
          },
        },
      ];
    },
  },
});
</script>

<template>
  <q-item-section
    v-for="(section, i) in props.sections"
    v-show="!section.hide"
    :key="i"
    :class="section.class"
    :side="section.side"
  >
    <slot :name="`section-${section.key}`" v-bind="item">
      <q-avatar v-if="section.img" size="40px">
        <q-img :src="section.img.src(item)" />
      </q-avatar>
      <q-item-label
        v-else-if="section.badge"
        :caption="section?.label?.caption"
        :class="
          section?.label?.class && typeof section?.label?.class === `function`
            ? section?.label?.class(item[section.key])
            : section?.label?.class
        "
      >
        <q-badge rounded :color="section.badge?.color(item)">
          <q-icon v-if="section.badge.icon" :name="section.badge.icon(item)" />
          {{
            section?.label?.format
              ? section.label.format(item[section.key])
              : item[section.key]
          }}
        </q-badge>
      </q-item-label>
      <q-item-label
        v-else
        :caption="section?.label?.caption"
        :class="
          section?.label?.class && typeof section?.label?.class === `function`
            ? section?.label?.class(item[section.key])
            : section?.label?.class
        "
      >
        {{
          section?.label?.format
            ? section.label.format(item[section.key])
            : item[section.key]
        }}
      </q-item-label>
    </slot>
  </q-item-section>
</template>
