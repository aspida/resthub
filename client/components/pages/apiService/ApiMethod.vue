<template>
  <div class="api-container">
    <div
      :class="['api-title', isOpened && 'is-opened']"
      @click="isOpened = !isOpened"
    >
      <div
        v-if="flatPathsObj.method"
        :class="[flatPathsObj.method, 'method']"
        :style="`background-color: ${methodColors[flatPathsObj.method]};`"
      >
        {{ flatPathsObj.method }}
      </div>
      <div class="api-path">
        {{ flatPathsObj.path }}
      </div>
      <p v-if="existsKey(flatPathsObj.opeObj, 'summary')" class="summary">
        {{ flatPathsObj.opeObj.summary }}
      </p>
      <div class="v-input__icon v-input__icon--append menu-down-wrapper">
        <i class="v-icon notranslate mdi mdi-menu-down theme--light"></i>
      </div>
    </div>
    <div v-if="isOpened" class="api-detail">
      <div
        v-if="existsKey(flatPathsObj.opeObj, 'description')"
        class="description"
      >
        <!-- eslint-disable-next-line vue/no-v-html -->
        <div v-html="$md.render(flatPathsObj.opeObj.description)" />
      </div>
      <div>
        <div class="sab-title">Parameters</div>
        <div v-if="existsKey(flatPathsObj.opeObj, 'parameters')">
          <api-parameters :parameters="flatPathsObj.opeObj.parameters" />
        </div>
        <div v-else>
          No Parameters
        </div>
      </div>
      <div v-if="existsKey(flatPathsObj.opeObj, 'requestBody')">
        <div class="sab-title">RequestBody</div>
        <api-request-body
          :app-title="appTitle"
          :method="flatPathsObj.method"
          :path="flatPathsObj.path"
          :parameters="flatPathsObj.opeObj.parameters || []"
          :request-body="flatPathsObj.opeObj.requestBody"
        />
      </div>
      <div>
        <div class="sab-title">Example: Request statement by Aspida</div>
        <example-using-aspida
          :app-title="appTitle"
          :method="flatPathsObj.method"
          :path="flatPathsObj.path"
          :parameters="flatPathsObj.opeObj.parameters || []"
          :request-body="flatPathsObj.opeObj.requestBody"
        />
      </div>
      <div v-if="existsKey(flatPathsObj.opeObj, 'responses')">
        <div class="sab-title">Responses</div>
        <table style="width: 100%;">
          <tr>
            <th style="width: 20%;">StatusCode</th>
            <th style="width: 80%;">Description</th>
          </tr>
          <tr v-for="(response, index) in arrOfResponse" :key="index">
            <td>
              {{ response.statusCode }}
            </td>
            <td>
              {{ response.responseObj.description }}
              <api-response
                v-if="existsKey(response.responseObj, 'content')"
                :status-code="response.statusCode"
                :response-obj="response.responseObj"
              />
            </td>
          </tr>
        </table>
      </div>
    </div>
  </div>
</template>
<script>
import { METHOD_COLORS } from '~/utils/constants'
import ApiRequestBody from '~/components/pages/apiService/ApiRequestBody.vue'
import ExampleUsingAspida from '~/components/pages/apiService/ExampleUsingAspida.vue'
import ApiParameters from '~/components/pages/apiService/ApiParameters.vue'
import ApiResponse from '~/components/pages/apiService/ApiResponse.vue'

// This script don't use TypeScript temporarily.
export default {
  components: {
    ApiParameters,
    ApiRequestBody,
    ExampleUsingAspida,
    ApiResponse
  },
  props: {
    flatPathsObj: {
      type: Object,
      required: true
    },
    appTitle: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      isOpened: false,
      methodColors: METHOD_COLORS
    }
  },
  computed: {
    arrOfResponse() {
      return Object.entries(this.flatPathsObj.opeObj.responses).map((e) => {
        return { statusCode: e[0], responseObj: e[1] }
      })
    }
  },
  methods: {
    existsKey: (obj, key) => {
      return Object.keys(obj).includes(key)
    },
    isString: (obj) => {
      return obj === 'string'
    },
    isBoolean: (obj) => {
      return obj === 'boolean'
    },
    hasEnum: (obj) => {
      return Object.keys(obj).includes('enum')
    }
  }
}
</script>
<style scoped>
.sab-title {
  padding: 12px 0 10px;
  font-size: 1em;
  font-weight: 500;
}
.api-container {
  margin-top: -1px;
  font-size: 11pt;
  color: #646464;
  border: thin solid #c0c0c0;
}
.api-title {
  display: grid;
  grid-template-columns: 50px 1fr 1fr 30px;
  align-self: center;
  padding: 7px 9px;
  cursor: pointer;
}
.is-opened {
  border-bottom: thin solid #c0c0c0;
}
.menu-down-wrapper {
  grid-column: 4/5;
}
.is-opened .mdi-menu-down {
  transform: rotate(180deg);
}
.api-detail {
  padding: 5px 20px;
}
.method {
  grid-column: 1/2;
  width: 52px;
  height: 22px;
  font-size: 0.83em;
  font-weight: 600;
  color: #fff;
  text-align: center;
  border-radius: 5px;
}
.api-path {
  grid-column: 2/3;
  margin-left: 12px;
  font-weight: 600;
}
.summary {
  grid-column: 3/4;
  margin-bottom: unset;
}
table {
  font-size: 13px;
  table-layout: fixed;
  border-collapse: collapse;
}
</style>
