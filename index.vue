<style scoped>
.sr-only {
  display: none
}
.pagination__button--page:disabled .sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  overflow: hidden;
  white-space: nowrap;
  border: 0;
  clip: rect(0, 0, 0, 0);
  clip-path: inset(50%)
}
.pagination {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
  justify-content: center;
  margin: 0;
  padding: 0;
  list-style-type: none
}
button {
  background: none;
  border: none
}
button:not(:disabled) {
  cursor: pointer
}
</style>

<template>
  <ul v-if="pageCount > 1" class="pagination">
    <li>
      <button
        type="button"
        aria-label="Previous Page"
        :tabindex="firstPageSelected() ? -1 : 0"
        :disabled="firstPageSelected()"
        class="pagination__button pagination__button--previous"
        @click="goToPreviousPage()"
      >
        Prev
      </button>
    </li>

    <li v-for="(page, idx) in pages" :key="idx">
      <span
        v-if="page.breakView"
        aria-label="Pages hidden"
        tabindex="-1"
        aria-hidden="true"
        class="pagination__break-view"
      >
        <slot name="breakViewContent">...</slot>
      </span>
      <button
        v-else
        :disabled="page.selected"
        type="button"
        :aria-label="'Page' + ' ' + page.content"
        :aria-current="page.selected"
        :tabindex="page.selected ? '-1' : '0'"
        :aria-hidden="page.selected ? 'true' : 'false'"
        class="pagination__button pagination__button--page"
        @click="goToPage(page.content)"
      >
        {{ page.content }} <span class="sr-only">(current)</span>
      </button>
    </li>

    <li>
      <button
        type="button"
        aria-label="Next Page"
        :tabindex="lastPageSelected() ? -1 : 0"
        :disabled="lastPageSelected()"
        class="pagination__button pagination__button--next"
        @click="goToNextPage()"
      >
        Next
      </button>
    </li>
  </ul>
</template>

<script>
export default {
  props: {
    pageCount: {
      type: Number,
      required: true
    },
    currentPage: {
      type: Number,
      default: 1
    },
    goToPage: {
      type: Function,
      required: true
    }
  },
  computed: {
    pages () {
      const items = {}
      if (this.pageCount <= 3) {
        for (let index = 0; index < this.pageCount; index++) {
          const page = {
            index,
            content: index + 1,
            selected: index === (this.currentPage - 1)
          }
          items[index] = page
        }
      } else {
        const halfPageRange = Math.floor(3 / 2)

        const setPageItem = (index) => {
          const page = {
            index,
            content: index + 1,
            selected: index === (this.currentPage - 1)
          }

          items[index] = page
        }

        const setBreakView = (index) => {
          const breakView = {
            disabled: true,
            breakView: true
          }

          items[index] = breakView
        }

        // 1st - loop thru low end of margin pages
        for (let i = 0; i < 1; i++) {
          setPageItem(i)
        }

        // 2nd - loop thru selected range
        let selectedRangeLow = 0
        if (this.currentPage - halfPageRange > 0) {
          selectedRangeLow = this.currentPage - 1 - halfPageRange
        }

        let selectedRangeHigh = selectedRangeLow + 2
        if (selectedRangeHigh >= this.pageCount) {
          selectedRangeHigh = this.pageCount - 1
          selectedRangeLow = selectedRangeHigh - 4
        }

        for (let i = selectedRangeLow; i <= selectedRangeHigh && i <= this.pageCount - 1; i++) {
          setPageItem(i)
        }

        // Check if there is breakView in the left of selected range
        if (selectedRangeLow > 1) {
          setBreakView(selectedRangeLow - 1)
        }

        // Check if there is breakView in the right of selected range
        if (selectedRangeHigh + 1 < this.pageCount - 1) {
          setBreakView(selectedRangeHigh + 1)
        }

        // 3rd - loop thru high end of margin pages
        for (let i = this.pageCount - 1; i >= this.pageCount - 1; i--) {
          setPageItem(i)
        }
      }
      return items
    }
  },
  methods: {
    firstPageSelected () {
      return this.currentPage === 1
    },
    lastPageSelected () {
      return (this.currentPage === this.pageCount) || (this.pageCount === 0)
    },
    goToPreviousPage () {
      if (!this.firstPageSelected()) {
        this.goToPage(this.currentPage - 1)
      }
    },
    goToNextPage () {
      if (!this.lastPageSelected()) {
        this.goToPage(this.currentPage + 1)
      }
    }
  }
}
</script>
