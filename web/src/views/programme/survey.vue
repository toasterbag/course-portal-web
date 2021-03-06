<template lang="pug">
.container
  div(v-if="this.ready")
    .row.justify-content-center.border-bottom(v-for="charts in chart_grid")
      .col-12.p-3(v-for="chart in charts")
        .fs-3 {{ chart.title }}
        .text-muted {{ chart.subtitle }}
        survey-bar-chart(
          :labels="labels",
          :means="chart.means",
          :medians="chart.medians",
          :display-mean="display_mean_values"
        )
</template>

<script>
import Http from "../../plugins/http";

export default {
  name: "course-surveys",
  data: () => ({
    ready: false,
    statistics_by_year: [],
  }),

  computed: {
    labels() {
      return this.statistics_by_year.map((s) => s.academic_year);
    },
    chart_grid() {
      return this.charts.groupInto(2);
    },
    charts() {
      return [
        {
          title: "Overall impression",
          subtitle: "What is your overall impression of the course?",
          median_key: "total_impression_median",
          mean_key: "total_impression_mean",
        },
        {
          title: "Prerequisites",
          subtitle:
            "I had enough prior knowledge to be able to follow the course",
          median_key: "prerequisite_median",
          mean_key: "prerequisite_mean",
        },
        {
          title: "Learning outcomes",
          subtitle:
            "The learning outcomes (see course plan) clearly describe what I was expected to learn in the course",
          median_key: "goals_median",
          mean_key: "goals_mean",
        },
        {
          title: "Structure",
          subtitle:
            "The course structure (as divided into lectures, exercises, lab sessions, simulations etc.) is appropriate in order to reach the intended learning outcome of the course",
          median_key: "course_structure_median",
          mean_key: "course_structure_mean",
        },
        {
          title: "Teaching",
          subtitle: "The teaching worked well",
          median_key: "course_teaching_median",
          mean_key: "course_teaching_mean",
        },
        {
          title: "Course litterature",
          subtitle:
            "The course literature (including other course material) supported the learning well",
          median_key: "course_litterature_median",
          mean_key: "course_litterature_mean",
        },
        {
          title: "Assessment",
          subtitle:
            "The assessment (including all compulsory elements, exams, assignments etc.) tested whether I had reached the intended learning outcomes of the course",
          median_key: "examination_median",
          mean_key: "examination_mean",
        },
        {
          title: "Course administration",
          subtitle:
            "The course administration (information during the course, course memo, course homepage etc.) worked well",
          median_key: "administration_median",
          mean_key: "administration_mean",
        },
        {
          title: "Workload",
          subtitle:
            "The course workload as related to the number of credits was...",
          median_key: "administration_median",
          mean_key: "workload_mean",
        },
        {
          title: "Working environment",
          subtitle:
            "The organization, content and teaching of this course have been designed and executed so that everyone can feel included, welcome and seen",
          median_key: "working_environment_median",
          mean_key: "working_environment_mean",
        },
      ].map(({ title, subtitle, mean_key }) => ({
        title,
        subtitle,
        medians: [],
        means: this.statistics_by_year.map((s) =>
          (s[mean_key] ?? 0).roundTo(2)
        ),
      }));
    },
  },
  created() {
    this.loadData();
  },
  methods: {
    async loadData() {
      this.statistics_by_year = await Http.get(
        `programme/${this.$route.params.code}/surveys`
      );

      this.ready = true;
    },
  },
};
</script>

<style lang="scss" scoped></style>
