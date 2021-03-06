<template>
  <div class="api-docs">
    <div class="api-docs__header">
      <a class="api-docs__logo">
        <img src="@baserow/modules/core/static/img/logo.svg" alt="" />
      </a>
      <a
        ref="databasesToggle"
        class="api-docs__switch"
        @click.prevent="databasesOpen = !databasesOpen"
      >
        <i class="api-docs__switch-icon fas fa-database"></i>
        {{ database.name }} database API documentation
      </a>
      <div class="api-docs__open">
        <nuxt-link
          v-if="database.tables.length > 0"
          :to="{
            name: 'database-table',
            params: {
              databaseId: database.id,
              tableId: database.tables[0].id,
            },
          }"
          class="button button--ghost"
          >open database</nuxt-link
        >
      </div>
    </div>
    <div v-show="databasesOpen" ref="databases" class="api-docs__databases">
      <div class="api-docs__databases-inner">
        <APIDocsSelectDatabase :selected="database.id"></APIDocsSelectDatabase>
      </div>
    </div>
    <div class="api-docs__nav">
      <ul class="api-docs__nav-list">
        <li>
          <a
            class="api-docs__nav-link"
            :class="{ active: navActive === 'section-introduction' }"
            @click.prevent="navigate('section-introduction')"
            >Introduction</a
          >
        </li>
        <li>
          <a
            class="api-docs__nav-link"
            :class="{ active: navActive === 'section-authentication' }"
            @click.prevent="navigate('section-authentication')"
            >Authentication</a
          >
        </li>
        <li v-for="table in database.tables" :key="table.id">
          <a
            class="api-docs__nav-link"
            :class="{ active: navActive === 'section-table-' + table.id }"
            @click.prevent="navigate('section-table-' + table.id)"
            >{{ table.name }} table <small>(id: {{ table.id }})</small></a
          >
          <ul
            class="api-docs__nav-sub"
            :class="{
              open:
                navActive === 'section-table-' + table.id ||
                navActive === 'section-table-' + table.id + '-fields' ||
                navActive === 'section-table-' + table.id + '-list' ||
                navActive === 'section-table-' + table.id + '-get' ||
                navActive === 'section-table-' + table.id + '-create' ||
                navActive === 'section-table-' + table.id + '-update' ||
                navActive === 'section-table-' + table.id + '-delete',
            }"
          >
            <li>
              <a
                class="api-docs__nav-link"
                :class="{
                  active: navActive === 'section-table-' + table.id + '-fields',
                }"
                @click.prevent="
                  navigate('section-table-' + table.id + '-fields')
                "
                >Fields</a
              >
            </li>
            <li>
              <a
                class="api-docs__nav-link"
                :class="{
                  active: navActive === 'section-table-' + table.id + '-list',
                }"
                @click.prevent="navigate('section-table-' + table.id + '-list')"
                >List rows</a
              >
            </li>
            <li>
              <a
                class="api-docs__nav-link"
                :class="{
                  active: navActive === 'section-table-' + table.id + '-get',
                }"
                @click.prevent="navigate('section-table-' + table.id + '-get')"
                >Get row</a
              >
            </li>
            <li>
              <a
                class="api-docs__nav-link"
                :class="{
                  active: navActive === 'section-table-' + table.id + '-create',
                }"
                @click.prevent="
                  navigate('section-table-' + table.id + '-create')
                "
                >Create row</a
              >
            </li>
            <li>
              <a
                class="api-docs__nav-link"
                :class="{
                  active: navActive === 'section-table-' + table.id + '-update',
                }"
                @click.prevent="
                  navigate('section-table-' + table.id + '-update')
                "
                >Update row</a
              >
            </li>
            <li>
              <a
                class="api-docs__nav-link"
                :class="{
                  active: navActive === 'section-table-' + table.id + '-delete',
                }"
                @click.prevent="
                  navigate('section-table-' + table.id + '-delete')
                "
                >Delete row</a
              >
            </li>
          </ul>
        </li>
        <li>
          <a
            class="api-docs__nav-link"
            :class="{ active: navActive === '#section-errors' }"
            @click.prevent="navigate('section-errors')"
            >Errors</a
          >
        </li>
      </ul>
    </div>
    <div ref="body" class="api-docs__body">
      <div class="api-docs__item">
        <div class="api-docs__left">
          <h2 id="section-introduction" class="api-docs__heading-2">
            Introduction
          </h2>
          <p class="api-docs__content">
            The {{ database.name }} database provides an easy way to integrate
            the data with any external system. The API follows REST semantics,
            uses JSON to encode objects and relies on standard HTTP codes,
            machine and human readable errors to signal operation outcomes.
          </p>
          <p class="api-docs__content">
            This documentation is generated automatically based the tables and
            fields that are in your database. If you make changes to your
            database, table or fields it could be that the API interface has
            also changed. Therefore, make sure that you update your API
            implementation accordingly.
          </p>
          <p class="api-docs__content">
            The ID of this database is:
            <code class="api-docs__code">{{ database.id }}</code>
            <br />
            Javascript example API client:
            <a href="https://github.com/axios/axios" target="_blank">axios</a>
            <br />
            Python example API client:
            <a href="https://requests.readthedocs.io/en/master/" target="_blank"
              >requests</a
            >
          </p>
        </div>
      </div>
      <div class="api-docs__item">
        <div class="api-docs__left">
          <h2 id="section-authentication" class="api-docs__heading-2">
            Authentication
          </h2>
          <p class="api-docs__content">
            Baserow uses a simple token based authentication. You need to
            generate at least one API token in your
            <a @click.prevent="$refs.settingsModal.show('tokens')">settings</a>
            to use the endpoints described below. It is possible to give create,
            read, update and delete permissions up until table level per token.
            You can authenticate to the API by providing your API token in the
            HTTP authorization bearer token header. All API requests must be
            authenticated and made over HTTPS.
          </p>
        </div>
        <div class="api-docs__right">
          <APIDocsExample
            v-model="exampleType"
            :url="$env.PUBLIC_BACKEND_URL"
            type=""
          ></APIDocsExample>
        </div>
      </div>
      <div v-for="table in database.tables" :key="table.id">
        <div class="item">
          <div class="api-docs__left">
            <h2 :id="'section-table-' + table.id">{{ table.name }} table</h2>
            <p class="api-docs__content">
              The ID of this table is:
              <code class="api-docs__code">{{ table.id }}</code>
            </p>
            <h3
              :id="'section-table-' + table.id + '-fields'"
              class="api-docs__heading-3"
            >
              Fields
            </h3>
            <p class="api-docs__content">
              Each row in the {{ table.name }} table contains the following
              fields.
            </p>
            <table class="api-docs__table">
              <thead>
                <tr>
                  <th>ID</th>
                  <th>Field name</th>
                  <th>Type</th>
                  <th>Description</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="field in fields[table.id]" :key="field.id">
                  <td>field_{{ field.id }}</td>
                  <td>{{ field.name }}</td>
                  <td>{{ field.type }}</td>
                  <td>
                    <code class="api-docs__code margin-bottom-1">
                      {{ field._.type }}
                    </code>
                    <br />
                    {{ field._.description }}
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
        <div class="api-docs__item">
          <div class="api-docs__left">
            <h3
              :id="'section-table-' + table.id + '-list'"
              class="api-docs__heading-3"
            >
              List rows
            </h3>
            <p class="api-docs__content">
              To list rows in the {{ table.name }} table a
              <code class="api-docs__code">GET</code> request as to be made to
              the {{ table.name }} endpoint. The response is paginated and by
              default the first page is returned. The correct page can be
              fetched by providing the
              <code class="api-docs__code">page</code> and
              <code class="api-docs__code">size</code> query parameters.
            </p>
            <h4 class="api-docs__heading-4">Query parameters</h4>
            <ul class="api-docs__parameters">
              <APIDocsParameter
                name="page"
                :optional="true"
                type="integer"
                standard="1"
                description="Defines which page of rows should be returned."
              ></APIDocsParameter>
              <APIDocsParameter
                name="size"
                :optional="true"
                type="integer"
                standard="100"
                description="Defines how many rows should be returned per page."
              ></APIDocsParameter>
              <APIDocsParameter
                name="search"
                :optional="true"
                type="string"
                standard="''"
                description="If provided only rows with data that matches the search query are going to be returned."
              ></APIDocsParameter>
            </ul>
          </div>
          <div class="api-docs__right">
            <APIDocsExample
              v-model="exampleType"
              type="GET"
              :url="getListURL(table)"
              :response="{
                count: 1024,
                next: getListURL(table) + '?page=2',
                previous: null,
                results: [getResponseItem(table)],
              }"
            ></APIDocsExample>
          </div>
        </div>
        <div class="api-docs__item">
          <div class="api-docs__left">
            <h3
              :id="'section-table-' + table.id + '-get'"
              class="api-docs__heading-3"
            >
              Get row
            </h3>
            <p class="api-docs__content">
              Fetch a single {{ table.name }} row.
            </p>
            <h4 class="api-docs__heading-4">Path parameters</h4>
            <ul class="api-docs__parameters">
              <APIDocsParameter
                name="row_id"
                type="integer"
                description="The unique identifier or the row that is requested."
              ></APIDocsParameter>
            </ul>
          </div>
          <div class="api-docs__right">
            <APIDocsExample
              v-model="exampleType"
              type="GET"
              :url="getItemURL(table)"
              :response="getResponseItem(table)"
            ></APIDocsExample>
          </div>
        </div>
        <div class="api-docs__item">
          <div class="api-docs__left">
            <h3
              :id="'section-table-' + table.id + '-create'"
              class="api-docs__heading-3"
            >
              Create row
            </h3>
            <p class="api-docs__content">Create a new {{ table.name }} row.</p>
            <h4 class="api-docs__heading-4">Request body schema</h4>
            <ul class="api-docs__parameters">
              <APIDocsParameter
                v-for="field in fields[table.id]"
                :key="field.id"
                :name="'field_' + field.id"
                :optional="true"
                :type="field._.type"
                :description="field._.description"
              ></APIDocsParameter>
            </ul>
          </div>
          <div class="api-docs__right">
            <APIDocsExample
              v-model="exampleType"
              type="POST"
              :url="getListURL(table)"
              :request="getRequestItem(table)"
              :response="getResponseItem(table)"
            ></APIDocsExample>
          </div>
        </div>
        <div class="api-docs__item">
          <div class="api-docs__left">
            <h3
              :id="'section-table-' + table.id + '-update'"
              class="api-docs__heading-3"
            >
              Update row
            </h3>
            <p class="api-docs__content">
              Updates an existing {{ table.name }} row.
            </p>
            <h4 class="api-docs__heading-4">Path parameters</h4>
            <ul class="api-docs__parameters">
              <APIDocsParameter
                name="row_id"
                type="integer"
                description="The unique identifier or the row that needs to be updated."
              ></APIDocsParameter>
            </ul>
            <h4 class="api-docs__heading-4">Request body schema</h4>
            <ul class="api-docs__parameters">
              <APIDocsParameter
                v-for="field in fields[table.id]"
                :key="field.id"
                :name="'field_' + field.id"
                :optional="true"
                :type="field._.type"
                :description="field._.description"
              ></APIDocsParameter>
            </ul>
          </div>
          <div class="api-docs__right">
            <APIDocsExample
              v-model="exampleType"
              type="PATCH"
              :url="getItemURL(table)"
              :request="getRequestItem(table)"
              :response="getResponseItem(table)"
            ></APIDocsExample>
          </div>
        </div>
        <div class="api-docs__item">
          <div class="api-docs__left">
            <h3
              :id="'section-table-' + table.id + '-delete'"
              class="api-docs__heading-3"
            >
              Delete row
            </h3>
            <p class="api-docs__content">
              Deletes an existing {{ table.name }} row.
            </p>
            <h4 class="api-docs__heading-4">Path parameters</h4>
            <ul class="api-docs__parameters">
              <APIDocsParameter
                name="row_id"
                type="integer"
                description="The unique identifier or the row that needs to be deleted."
              ></APIDocsParameter>
            </ul>
          </div>
          <div class="api-docs__right">
            <APIDocsExample
              v-model="exampleType"
              type="DELETE"
              :url="getItemURL(table)"
            ></APIDocsExample>
          </div>
        </div>
      </div>
      <div class="api-docs__item">
        <div class="api-docs__left">
          <h2 id="section-errors" class="api-docs__heading-2">
            HTTP Errors
          </h2>
          <table class="api-docs__table">
            <thead>
              <tr>
                <th>Error code</th>
                <th>Name</th>
                <th>Description</th>
              </tr>
            </thead>
            <tbody>
              <tr>
                <td>200</td>
                <td>Ok</td>
                <td>Request completed successfully.</td>
              </tr>
              <tr>
                <td>400</td>
                <td>Bad request</td>
                <td>
                  The request contains invalid values or the JSON could not be
                  parsed.
                </td>
              </tr>
              <tr>
                <td>401</td>
                <td>Unauthorized</td>
                <td>
                  When you try to access an endpoint without valid token.
                </td>
              </tr>
              <tr>
                <td>404</td>
                <td>Not found</td>
                <td>Row or table is not found.</td>
              </tr>
              <tr>
                <td>413</td>
                <td>Request Entity Too Large</td>
                <td>
                  The request exceeded the maximum allowed payload size.
                </td>
              </tr>
              <tr>
                <td>500</td>
                <td>Internal Server Error</td>
                <td>The server encountered an unexpected condition.</td>
              </tr>
              <tr>
                <td>502</td>
                <td>Bad gateway</td>
                <td>
                  Baserow is restarting or an unexpected outage is in progress.
                </td>
              </tr>
              <tr>
                <td>503</td>
                <td>Service unavailable</td>
                <td>The server could not process your request in time.</td>
              </tr>
            </tbody>
          </table>
        </div>
        <div class="api-docs__right">
          <APIDocsExample
            v-model="exampleType"
            :url="$env.PUBLIC_BACKEND_URL"
            type=""
            :response="{
              error: 'ERROR_NO_PERMISSION_TO_TABLE',
              description: 'he token does not have permissions to the table.',
            }"
          ></APIDocsExample>
        </div>
      </div>
    </div>
    <SettingsModal ref="settingsModal"></SettingsModal>
  </div>
</template>

<script>
import { isElement } from '@baserow/modules/core/utils/dom'
import SettingsModal from '@baserow/modules/core/components/settings/SettingsModal'
import APIDocsExample from '@baserow/modules/database/components/docs/APIDocsExample'
import APIDocsParameter from '@baserow/modules/database/components/docs/APIDocsParameter'
import APIDocsSelectDatabase from '@baserow/modules/database/components/docs/APIDocsSelectDatabase'
import { DatabaseApplicationType } from '@baserow/modules/database/applicationTypes'
import FieldService from '@baserow/modules/database/services/field'

export default {
  name: 'APIDocsDatabase',
  components: {
    SettingsModal,
    APIDocsExample,
    APIDocsParameter,
    APIDocsSelectDatabase,
  },
  middleware: ['authenticated', 'groupsAndApplications'],
  async asyncData({ store, params, error, app }) {
    const databaseId = parseInt(params.databaseId)
    const database = store.getters['application/get'](databaseId)
    const type = DatabaseApplicationType.getType()

    if (database === undefined || database.type !== type) {
      return error({ statusCode: 404, message: 'Database not found.' })
    }

    const fields = {}
    const populateField = (field) => {
      const fieldType = app.$registry.get('field', field.type)
      field._ = {
        type: fieldType.getDocsDataType(field),
        description: fieldType.getDocsDescription(field),
        requestExample: fieldType.getDocsRequestExample(field),
        responseExample: fieldType.getDocsResponseExample(field),
      }
      return field
    }

    for (const i in database.tables) {
      const table = database.tables[i]
      const { data } = await FieldService(app.$client).fetchAll(table.id)
      fields[table.id] = data.map((field) => populateField(field))
    }

    return { database, fields }
  },
  data() {
    return {
      // Indicates which request example type is shown.
      exampleType: 'curl',
      // Indicates which navigation item is active.
      navActive: '',
      // Indicates if the databases sidebar is open.
      databasesOpen: false,
    }
  },
  mounted() {
    // When the user clicks outside the databases sidebar and it is open then it must
    // be closed.
    this.$el.clickOutsideEvent = (event) => {
      if (
        this.databasesOpen &&
        !isElement(this.$refs.databasesToggle, event.target) &&
        !isElement(this.$refs.databases, event.target)
      ) {
        this.databasesOpen = false
      }
    }
    document.body.addEventListener('click', this.$el.clickOutsideEvent)

    // When the user scrolls in the body or when the window is resized, the active
    // navigation item must be updated.
    this.$el.scrollEvent = () => this.updateNav()
    this.$el.resizeEvent = () => this.updateNav()
    this.$refs.body.addEventListener('scroll', this.$el.scrollEvent)
    window.addEventListener('resize', this.$el.resizeEvent)
    this.updateNav()
  },
  beforeDestroy() {
    document.body.removeEventListener('click', this.$el.clickOutsideEvent)
    this.$refs.body.removeEventListener('scroll', this.$el.scrollEvent)
    window.removeEventListener('resize', this.$el.resizeEvent)
  },
  methods: {
    /**
     * Called when the user scrolls or when the window is resized. It will check which
     * navigation item is active based on the scroll position of the available ids.
     */
    updateNav() {
      const body = this.$refs.body
      const sections = body.querySelectorAll('[id]')
      const margin = 40
      sections.forEach((section, index) => {
        const top = section.offsetTop - margin
        const nextIndex = (index + 1).toString()
        const next =
          nextIndex in sections
            ? sections[nextIndex].offsetTop - margin
            : body.scrollHeight
        if (top <= body.scrollTop && body.scrollTop < next) {
          this.navActive = section.id
        }
      })
    },
    navigate(to) {
      const section = this.$refs.body.querySelector(`[id='${to}']`)
      this.$refs.body.scrollTop = section.offsetTop - 20
    },
    /**
     * Generates an example request object based on the available fields of the table.
     */
    getRequestItem(table, response = false) {
      const item = {}
      this.fields[table.id].forEach((field) => {
        const example = response
          ? field._.responseExample
          : field._.requestExample
        item[`field_${field.id}`] = example
      })
      return item
    },
    /**
     * Generates an example response object based on the available fields of the table.
     */
    getResponseItem(table) {
      const item = { id: 0 }
      Object.assign(item, this.getRequestItem(table, true))
      return item
    },
    getListURL(table) {
      return `${this.$env.PUBLIC_BACKEND_URL}/api/database/rows/table/${table.id}/`
    },
    getItemURL(table) {
      return this.getListURL(table) + '{row_id}/'
    },
  },
}
</script>
