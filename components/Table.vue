import { duration } from 'moment'; import { cvTime } from '~/static/time';
import { cvTime } from '~/static/time'; import { duration } from 'moment';
<template>
  <div
    class="relative overflow-x-auto shadow-md sm:rounded-lg w-[80vw] mt-8 mx-auto"
  >
    <h3
      class="text-center text-2xl font-medium text-gray-900 dark:text-white mb-6"
    >
      Activities
    </h3>
    <table class="w-full text-sm text-left text-gray-500 dark:text-gray-400">
      <thead
        class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400"
      >
        <tr>
          <th scope="col" class="px-6 py-3">Action</th>
          <th scope="col" class="px-6 py-3">
            <div class="flex items-center">
              Duration
              <!-- <a href="#"
                ><svg
                  class="w-3 h-3 ml-1.5"
                  aria-hidden="true"
                  xmlns="http://www.w3.org/2000/svg"
                  fill="currentColor"
                  viewBox="0 0 24 24"
                >
                  <path
                    d="M8.574 11.024h6.852a2.075 2.075 0 0 0 1.847-1.086 1.9 1.9 0 0 0-.11-1.986L13.736 2.9a2.122 2.122 0 0 0-3.472 0L6.837 7.952a1.9 1.9 0 0 0-.11 1.986 2.074 2.074 0 0 0 1.847 1.086Zm6.852 1.952H8.574a2.072 2.072 0 0 0-1.847 1.087 1.9 1.9 0 0 0 .11 1.985l3.426 5.05a2.123 2.123 0 0 0 3.472 0l3.427-5.05a1.9 1.9 0 0 0 .11-1.985 2.074 2.074 0 0 0-1.846-1.087Z"
                  /></svg
              ></a> -->
            </div>
          </th>
          <th scope="col" class="px-6 py-3">
            <div class="flex items-center">
              Date
              <!-- <a href="#"
                ><svg
                  class="w-3 h-3 ml-1.5"
                  aria-hidden="true"
                  xmlns="http://www.w3.org/2000/svg"
                  fill="currentColor"
                  viewBox="0 0 24 24"
                >
                  <path
                    d="M8.574 11.024h6.852a2.075 2.075 0 0 0 1.847-1.086 1.9 1.9 0 0 0-.11-1.986L13.736 2.9a2.122 2.122 0 0 0-3.472 0L6.837 7.952a1.9 1.9 0 0 0-.11 1.986 2.074 2.074 0 0 0 1.847 1.086Zm6.852 1.952H8.574a2.072 2.072 0 0 0-1.847 1.087 1.9 1.9 0 0 0 .11 1.985l3.426 5.05a2.123 2.123 0 0 0 3.472 0l3.427-5.05a1.9 1.9 0 0 0 .11-1.985 2.074 2.074 0 0 0-1.846-1.087Z"
                  /></svg
              ></a> -->
            </div>
          </th>
          <th scope="col" class="px-6 py-3"></th>
          <th scope="col" class="px-6 py-3">
            <span class="sr-only">Edit</span>
          </th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="row in props.data"
          class="border-b border-gray-300"
          :style="`border-left: solid 20px ${getBackground(
            row.type,
          )}; height: ${getHeight(row.duration)}px;`"
        >
          <th
            scope="row"
            class="px-6 py-4 font-medium text-gray-900 whitespace-nowrap"
          >
            {{ row.name }}
          </th>
          <td class="px-6 py-4">{{ row.duration }}</td>
          <td class="px-6 py-4">{{ row.date }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
<script setup>
import { cvTime } from '~/static/time';
const props = defineProps({
  data: {
    type: Array,
    required: true,
  },
});

function getBackground(type) {
  return '#14b8a6';
}
function getHeight(duration) {
  let height = cvTime(duration) * 24 * 100;
  if (height > 300) height = 300;
  if (height < 50) height = 50;
  return height.toFixed(0).toString();
}
</script>
<style></style>
