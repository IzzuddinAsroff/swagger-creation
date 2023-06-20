<template>
  <div>
    <div class="gap-bottom">
      <b-row>
        <b-col sm="1"></b-col>
        <b-col sm="3">
          <div style="text-align: right;">
            <span><b>API Title : </b> </span>
          </div>
        </b-col>
        <b-col>
          <b-form-input class="width-field" v-model="title" type="text"></b-form-input>
        </b-col>
      </b-row>
    </div>
    <div class="gap-bottom">
      <b-row>
        <b-col sm="1"></b-col>
        <b-col sm="3">
          <div style="text-align: right;">
            <span><b>API path : </b> </span>
          </div>
        </b-col>
        <b-col>
          <b-form-input class="width-field" v-model="path" type="text"></b-form-input>
        </b-col>
      </b-row>
    </div>
    <div class="gap-bottom">
      <b-row>
        <b-col sm="1"></b-col>
        <b-col sm="3">
          <div style="text-align: right;">
            <span><b>API tags : </b> </span>
          </div>
        </b-col>
        <b-col>
          <b-form-input class="width-field" v-model="tag" type="text"></b-form-input>
        </b-col>
      </b-row>
    </div>

    <div class="gap-bottom">
      <b-row>
        <b-col sm="1"></b-col>
        <b-col sm="3">
          <div style="text-align: right;">
            <span><b>API methods : </b> </span>
          </div>
        </b-col>
        <b-col>
          <div style="text-align: left;">
            <b-form-select class="width-field" v-model="httpMethod" :options="methodOptions"></b-form-select>
          </div>

        </b-col>
      </b-row>
    </div>

    <div class="gap-bottom">
      <span><b>Description</b></span>
      <div>
        <b-row>
          <b-col sm="3"></b-col>
          <b-col>
            <b-form-textarea v-model="description" rows="10"></b-form-textarea>
          </b-col>
          <b-col sm="3"></b-col>
        </b-row>
      </div>
    </div>


    <div class="gap-bottom" v-if="httpMethod == 'post'">
      <span><b>Required request X-MB Header : </b></span>

      <b-form-group v-slot="{ ariaDescribedby }">
        <b-form-radio v-model="isEnableHeder" :aria-describedby="ariaDescribedby" name="some-radios"
          value=true>Yes</b-form-radio>
        <b-form-radio v-model="isEnableHeder" :aria-describedby="ariaDescribedby" name="some-radios"
          value=false>No</b-form-radio>
      </b-form-group>
    </div>

    <div class="row">
      <div class="col-1"></div>
      <div class="col-5">
        <span><b>Request body json</b></span>
        <div v-if="httpMethod == 'post'">
          <b-form-textarea v-model="requestBody" rows="15" cols="80"></b-form-textarea>
        </div>
        <div v-else>
          <div class="pt-3">
            <b-row>
              <b-col>
                <span><b>Number of parameter : </b> </span>
              </b-col>
              <b-col>
                <b-form-input class="width-field" v-model="parameterNumber" type="number"></b-form-input>
              </b-col>
            </b-row>
          </div>
          <div class="pt-4">
            <div class="pb-1" v-for="index in parseInt(getFieldNumber)" :key="index">
              <b-row>
                <b-col sm="2">
                  <span><b>Field name : </b> </span>
                </b-col>
                <b-col sm="4">
                  <b-form-input @blur="setFieldName($event.target.value, index)" type="text"></b-form-input>
                </b-col>
                <b-col sm="2">
                  <span><b>Field value : </b> </span>
                </b-col>
                <b-col sm="4">
                  <b-form-input @blur="setFieldValue($event.target.value, index)" type="text"></b-form-input>
                </b-col>
              </b-row>
            </div>
          </div>


        </div>

      </div>
      <div class="col-5">
        <span><b>Response body json</b></span>
        <div>
          <b-form-textarea v-model="responseBody" rows="15" cols="80"></b-form-textarea>
        </div>

      </div>
      <div class="col-1"></div>
    </div>

    <div class="row" style="padding-top: 1rem;">
      <div class="col-6" style="text-align: right;">
        <b-button variant="success" @click="generateSwagger()">Generate Swagger</b-button>
      </div>
      <div class="col-6" style="text-align: left;">
        <b-button variant="warning" @click="resetSwagger()">Reset Swagger</b-button>
      </div>


    </div>


    <div style="padding-top: 1rem;">
      <b>
        <h4>Generated swagger file</h4>
      </b>
      <div class="pb2"><b-button variant="primary" @click="copyWording()">Copy Swagger</b-button></div>
      <div class="pt-3">
        <textarea :value="changeJsonToString(swaggerFile)" id="swaggerFileBox" rows="20" cols="120" readonly></textarea>
      </div>
    </div>

  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  created() {
    const self = this

    self.copySwaggerFile = self.$_.cloneDeep(self.swaggerFile)
  },
  data() {
    return {
      title: "Corporate - Account Balance Inquiry",
      path: "/api/my/crp/v1/accountbalinq",
      tag: "Account Balance Inquiry",
      httpMethod: "post",
      description: "OK",
      isEnableHeder: false,
      copySwaggerFile: '',
      parameterNumber: 1,
      methodOptions: [{ value: "post", text: 'POST' },
      { value: 'get', text: 'GET' }],
      queryField: [],
      queryValue: [],
      testA: {
        "corporateId": "MYMAYBANK",
        "accountNo": "012345678910",
        "accountCCY": "USD",
        "accountType": "Savings",
        "provider": "MY",
        "messageId": "APIGWYYYYMMDDHHMMSSXXXXXX",
        "informations": [
          {
            "currency": "MYR",
            "value": 10,
            "sharingNumber": [1, 2, 3]
          }
        ]
      },
      testB: {
        "accountName": "ARISSA BAKERY",
        "accountCCY": "USD",
        "availBal": 100.1,
        "availBalSign": "C",
        "ledgerBal": 100.1,
        "ledgerBalSign": "C",
        "currentBal": 100.1,
        "currentBalSign": "C",
        "ownBal": 100.1,
        "ownBalSign": "C",
        "holdAmt": 100.1,
        "facilityLimit": 100.1,
        "odAmt": 100.1,
        "intraOd": 100.1,
        "floatOne": 100.1,
        "floatTwo": 100.1,
        "ocLateFloat": 100.1,
        "totalFloat": 100.1,
        "utilisedAmt": 100.1,
        "unutilisedAmt": 100.1
      },
      requestBody: "",
      responseBody: "",
      generatedReqBody: "",
      generatedResBody: "",
      swaggertRequestBody: {
        content: {
          "application/json": {
            schema: {
              type: "object",
              properties: {

              }
            }
          }

        }
      },
      swaggertResponseBody: {
        "200": {
          description: "OK",
          headers: {},
          content: {
            "application/json": {
              schema: {
                type: "object",
                properties: {

                }
              }
            }

          }
        }
      },
      headerReqParameter: [
        {
          "schema": {
            "type": "string"
          },
          "in": "header",
          "name": "X-MB-Timestamp",
          "example": 1678322759562,
          "description": "Current timestamp in epoch (milliseconds)",
          "required": true
        },
        {
          "schema": {
            "type": "string"
          },
          "in": "header",
          "name": "X-MB-API-KEY",
          "example": "XXXXXXXXXXXXXXX",
          "description": "Unique ID per Client ",
          "required": true
        },
        {
          "schema": {
            "type": "string"
          },
          "in": "header",
          "name": "X-MB-E2E-Id",
          "example": "XXXXXXXXXXXXXXX",
          "description": "End to end message id. For troubleshooting purpose, must be unique",
          "required": true
        },
        {
          "schema": {
            "type": "string"
          },
          "in": "header",
          "name": "X-MB-Authorization",
          "example": "XXXXXXXXXXXXXXXXXXXXX",
          "description": "{access_token}",
          "required": true
        },
        {
          "schema": {
            "type": "string"
          },
          "in": "header",
          "name": "X-MB-Client-Id",
          "example": "XXXXXXXXXXXXXXXXXXXXX",
          "description": "Unique Client Id per partner",
          "required": true
        },
        {
          "schema": {
            "type": "string"
          },
          "in": "header",
          "name": "X-MB-ENV",
          "example": "T",
          "description": "Environment Values",
          "required": true
        },
        {
          "schema": {
            "type": "string"
          },
          "in": "header",
          "name": "X-MB-Signature-Alg",
          "example": "RSA-SHA256",
          "description": "‘RSA-SHA256’",
          "required": true
        },
        {
          "schema": {
            "type": "string"
          },
          "in": "header",
          "name": "X-MB-Signed-Headers",
          "example": "X-MB-Client-Id;X-MB-Authorization;X-MB-Timestamp",
          "description": "‘X-MB-Client-Id;X-MB-Authorization;X-MB-Timestamp’",
          "required": true
        },
        {
          "schema": {
            "type": "string"
          },
          "in": "header",
          "example": "XXXXXXXXXXXXXXXXXXXXXXXXXXXX",
          "name": "X-MB-Signature-Value",
          "description": "Digital Signature",
          "required": true
        }
      ],
      headerResParameter: {
        "Content-Type": {
          "schema": {
            "type": "string"
          },
          "description": "Content type. Valid value is 'application/json'"
        },
        "X-MB-E2E-Id": {
          "schema": {
            "type": "string"
          },
          "description": "End to end message id. For troubleshooting purpose, must be unique"
        }
      },
      swaggerFile: {
        "openapi": "3.0.0",
        "info": {
          "title": "",
          "version": "1.0",
          description: ""
        },
        "servers": [
          {
            "url": "/apiV2/client"
          }
        ],
        "paths": {}
      },
      swaggerTest: {
        "openapi": "3.0.0",
        "x-stoplight": {
          "id": "eeo58c50lnkgb"
        },
        "info": {
          "title": "Corporate - Account Balance Inquiry",
          "version": "1.0"
        },
        "servers": [
          {
            "url": "/apiV2/client/",
            "description": "Staging server URL"
          }
        ],
        "paths": {
          "/api/my/crp/v1/accountbalinq": {
            "parameters": [],
            "post": {
              "tags": [
                "Account Balance Inquiry"
              ],
              "summary": "",
              "operationId": "post-api-my-crp-v1-accountbalinq",
              "responses": {
                "200": {
                  "description": "OK",
                  "headers": {
                    "Content-Type": {
                      "schema": {
                        "type": "string"
                      },
                      "description": "Content type. Valid value is 'application/json'"
                    },
                    "X-MB-E2E-Id": {
                      "schema": {
                        "type": "string"
                      },
                      "description": "End to end message id. For troubleshooting purpose, must be unique"
                    }
                  },
                  "content": {
                    "application/json": {
                      "schema": {
                        "properties": {
                          "accountName": {
                            "type": "string",
                            "example": "ARISSA BAKERY",
                            "maxLength": 70,
                            "description": "Account name"
                          },
                          "accountCCY": {
                            "type": "string",
                            "example": "USD",
                            "maxLength": 3,
                            "description": "Currency of the accounts in International format."
                          },
                          "availBal": {
                            "type": "number",
                            "example": "100.10",
                            "maximum": 20,
                            "description": "Accounts available balance with 2 decimal point."
                          },
                          "availBalSign": {
                            "type": "string",
                            "example": "C",
                            "maxLength": 1,
                            "description": "Accounts available balance sign."
                          },
                          "ledgerBal": {
                            "type": "number",
                            "maximum": 20,
                            "example": "100.10",
                            "description": "Accounts ledger balance with 2 decimal point."
                          },
                          "ledgerBalSign": {
                            "type": "string",
                            "example": "C",
                            "maxLength": 1,
                            "description": "Accounts ledger balance sign."
                          },
                          "currentBal": {
                            "type": "number",
                            "example": "100.10",
                            "maximum": 20,
                            "description": "Accounts current balance with 2 decimal point."
                          },
                          "currentBalSign": {
                            "type": "string",
                            "example": "C",
                            "maxLength": 1,
                            "description": "Accounts current balance sign."
                          },
                          "ownBal": {
                            "type": "number",
                            "maximum": 20,
                            "example": "100.10",
                            "description": "Accounts own balance with 2 decimal point."
                          },
                          "ownBalSign": {
                            "type": "string",
                            "maxLength": 1,
                            "example": "C",
                            "description": "Accounts own balance  sign."
                          },
                          "holdAmt": {
                            "type": "number",
                            "example": "100.10",
                            "maximum": 20,
                            "description": "Accounts earmark amount."
                          },
                          "facilityLimit": {
                            "type": "number",
                            "maximum": 20,
                            "example": "100.10",
                            "description": "Accounts authorised limit."
                          },
                          "odAmt": {
                            "type": "number",
                            "example": "100.10",
                            "maximum": 20,
                            "description": "Accounts drawing limit."
                          },
                          "intraOd": {
                            "type": "number",
                            "example": "100.10",
                            "maximum": 20,
                            "description": "Accounts assigned float"
                          },
                          "floatOne": {
                            "type": "number",
                            "example": "100.10",
                            "maximum": 20,
                            "description": "Accounts assigned float"
                          },
                          "floatTwo": {
                            "type": "number",
                            "example": "100.10",
                            "maximum": 20,
                            "description": "Accounts FRB float."
                          },
                          "ocLateFloat": {
                            "type": "number",
                            "example": "100.10",
                            "maximum": 20,
                            "description": "Accounts assigned float"
                          },
                          "totalFloat": {
                            "type": "number",
                            "maximum": 20,
                            "example": "100.10",
                            "description": "Accounts assigned float."
                          },
                          "utilisedAmt": {
                            "type": "number",
                            "example": "100.10",
                            "maximum": 20,
                            "description": "Accounts overdraft utilisation amount."
                          },
                          "unutilisedAmt": {
                            "type": "integer",
                            "example": "100.10",
                            "maximum": 20,
                            "description": "Accounts overdraft unutilisation amount."
                          }
                        }
                      }
                    }
                  }
                }
              },
              "parameters": [
                {
                  "schema": {
                    "type": "string"
                  },
                  "in": "header",
                  "name": "X-MB-Timestamp",
                  "example": 1678322759562,
                  "description": "Current timestamp in epoch (milliseconds)",
                  "required": true
                },
                {
                  "schema": {
                    "type": "string"
                  },
                  "in": "header",
                  "name": "X-MB-API-KEY",
                  "example": "XXXXXXXXXXXXXXX",
                  "description": "Unique ID per Client ",
                  "required": true
                },
                {
                  "schema": {
                    "type": "string"
                  },
                  "in": "header",
                  "name": "X-MB-E2E-Id",
                  "example": "XXXXXXXXXXXXXXX",
                  "description": "End to end message id. For troubleshooting purpose, must be unique",
                  "required": true
                },
                {
                  "schema": {
                    "type": "string"
                  },
                  "in": "header",
                  "name": "X-MB-Authorization",
                  "example": "XXXXXXXXXXXXXXXXXXXXX",
                  "description": "{access_token}",
                  "required": true
                },
                {
                  "schema": {
                    "type": "string"
                  },
                  "in": "header",
                  "name": "X-MB-Client-Id",
                  "example": "XXXXXXXXXXXXXXXXXXXXX",
                  "description": "Unique Client Id per partner",
                  "required": true
                },
                {
                  "schema": {
                    "type": "string"
                  },
                  "in": "header",
                  "name": "X-MB-ENV",
                  "example": "T",
                  "description": "Environment Values",
                  "required": true
                },
                {
                  "schema": {
                    "type": "string"
                  },
                  "in": "header",
                  "name": "X-MB-Signature-Alg",
                  "example": "RSA-SHA256",
                  "description": "‘RSA-SHA256’",
                  "required": true
                },
                {
                  "schema": {
                    "type": "string"
                  },
                  "in": "header",
                  "name": "X-MB-Signed-Headers",
                  "example": "X-MB-Client-Id;X-MB-Authorization;X-MB-Timestamp",
                  "description": "‘X-MB-Client-Id;X-MB-Authorization;X-MB-Timestamp’",
                  "required": true
                },
                {
                  "schema": {
                    "type": "string"
                  },
                  "in": "header",
                  "example": "XXXXXXXXXXXXXXXXXXXXXXXXXXXX",
                  "name": "X-MB-Signature-Value",
                  "description": "Digital Signature",
                  "required": true
                }
              ],
              "requestBody": {
                "content": {
                  "application/json": {
                    "schema": {
                      "type": "object",
                      "properties": {
                        "corporateId": {
                          "type": "string",
                          "example": "MYMAYBANK",
                          "maxLength": 20,
                          "description": "Corporate ID"
                        },
                        "accountNo": {
                          "type": "string",
                          "example": "012345678910",
                          "maxLength": 35,
                          "description": "All types of accounts (CCA, FCA, SA, FD, MMD, TF, TL) according to Country's availability"
                        },
                        "accountCCY": {
                          "type": "string",
                          "example": "USD",
                          "maxLength": 3,
                          "description": "Currency of the accounts in International format."
                        },
                        "accountType": {
                          "type": "string",
                          "example": "Savings",
                          "maxLength": 20,
                          "description": "Type of account"
                        },
                        "provider": {
                          "type": "string",
                          "example": "MY",
                          "maxLength": 2,
                          "description": "To identify where the account belongs. Country code"
                        },
                        "messageId": {
                          "type": "string",
                          "example": "APIGWYYYYMMDDHHMMSSXXXXXX",
                          "maxLength": 35,
                          "description": "Customer uniques reference id."
                        }
                      },
                      "required": [
                        "corporateId",
                        "accountNo",
                        "accountCCY",
                        "accountType",
                        "provider",
                        "messageId"
                      ]
                    }
                  }
                }
              }
            }
          }
        },
        "components": {
          "schemas": {}
        }
      }
    }
  },
  computed: {
    getFieldNumber() {
      return this.parameterNumber
    }
  },
  methods: {
    setFieldName(payload, index) {
      const self = this

      let obj = {
        index: index,
        name: payload
      }

      if (self.queryField.length < 0) {
        self.queryField.push(obj)
      } else {
        let findObj = self.queryField.find(el => {
          return el.index == index
        })

        if (findObj) {
          self.queryField.splice(index - 1, 1, obj);
        } else {
          self.queryField.push(obj)
        }
      }
    },
    setFieldValue(payload, index) {
      const self = this

      let obj = {
        index: index,
        name: payload
      }

      if (self.queryValue.length < 0) {
        self.queryValue.push(obj)
      } else {
        let findObj = self.queryValue.find(el => {
          return el.index == index
        })

        if (findObj) {
          self.queryValue.splice(index - 1, 1, obj);
        } else {
          self.queryValue.push(obj)
        }
      }
    },
    changeJsonToString(payload) {
      return JSON.stringify(payload)
    },
    resetSwagger() {
      const self = this

      self.requestBody = ""
      self.parameterNumber = 0
      self.queryField = []
      self.queryValue = []
      setTimeout(() => {
        self.parameterNumber = 1
      }, 150);

      self.responseBody = ""
      self.swaggerFile = self.copySwaggerFile
    },
    generateSwagger() {
      const self = this
      self.swaggerFile = self.$_.cloneDeep(self.copySwaggerFile)

      self.swaggerFile.info.title = self.title
      self.swaggerFile.info.description = self.description

      let jsonReqBody
      let jsonResBody
      if (self.httpMethod == 'post') {
        jsonReqBody = self.isJson(self.requestBody) ? JSON.parse(self.requestBody) : self.requestBody
        jsonResBody = self.isJson(self.responseBody) ? JSON.parse(self.responseBody) : self.responseBody

        self.generatedReqBody = self.generateBody(self.swaggertRequestBody.content["application/json"].schema.properties, jsonReqBody)
        self.generatedResBody = self.generateBody(self.swaggertResponseBody["200"].content["application/json"].schema.properties, jsonResBody)

        console.log("generatedReqBody", self.generatedReqBody);
        console.log("generatedResBody", self.generatedResBody);
        self.swaggertRequestBody.content["application/json"].schema.properties = self.generatedReqBody
        self.swaggertResponseBody["200"].content["application/json"].schema.properties = self.generatedResBody
        self.swaggertResponseBody["200"].headers = self.headerResParameter

        self.swaggerFile.paths[self.path] = {}
        self.swaggerFile.paths[self.path][self.httpMethod] = {
          tags: [self.tag],
          operationId: "",
          responses: self.swaggertResponseBody,
          requestBody: self.swaggertRequestBody
        }

        if (self.isEnableHeder) {
          self.swaggerFile.paths[self.path][self.httpMetsonResBodyhod].parameters = self.headerReqParameter
        }
      } else {
        let queryArray = []
        console.log('self.parameterNumber', self.parameterNumber);
        for (let index = 1; index <= self.parameterNumber; index++) {
          let findQueryName = self.queryField.find(el => {
            return el.index == index
          })

          let findQueryValue = self.queryValue.find(el => {
            return el.index == index
          })

          let objQuery = {
            name: findQueryName.name,
            "in": "query",
            "description": `Example value ${findQueryValue.name}`,
            schema: {
              type: typeof findQueryValue.name
            }
          }

          console.log(objQuery);

          queryArray.push(objQuery)
        }

        console.log(queryArray);

        jsonResBody = self.isJson(self.responseBody) ? JSON.parse(self.responseBody) : self.responseBody

        self.generatedResBody = self.generateBody(self.swaggertResponseBody["200"].content["application/json"].schema.properties, jsonResBody)

        console.log("generatedResBody", self.generatedResBody);
        self.swaggertResponseBody["200"].content["application/json"].schema.properties = self.generatedResBody
        self.swaggertResponseBody["200"].headers = self.headerResParameter

        self.swaggerFile.paths[self.path] = {}
        self.swaggerFile.paths[self.path][self.httpMethod] = {
          tags: [self.tag],
          operationId: "",
          responses: self.swaggertResponseBody,
          parameters: queryArray
        }
      }

      // console.warn(self.swaggerFile);
    },

    copyWording() {
      const element = document.querySelector('#swaggerFileBox');
      element.select();
      element.setSelectionRange(0, 99999);
      document.execCommand('copy');
    },

    isJson(str) {
      try {
        JSON.parse(str);
      } catch (e) {
        return false;
      }
      return true;
    },

    generateBody(objBody, payload) {
      const self = this

      for (const key in payload) {
        if (Array.isArray(payload[key])) {
          objBody[key] = {}
          objBody[key].type = "array"
          objBody[key].items = {}
          objBody[key].items.type = "object"
          objBody[key].items.properties = {}
          if (typeof payload[key][0] === 'object' && payload[key][0] !== null) {
            payload[key].forEach(el => {
              self.generateBody(objBody[key].items.properties, el)
            })
          } else {
            objBody[key] = self.generateArrayValue(objBody[key], key, payload[key])
          }

        } else {
          if (typeof payload[key] === 'object' && payload[key] !== null) {
            objBody[key] = {}
            objBody[key].type = "object"
            objBody[key].properties = {}
            for (const key2 in payload[key]) {
              objBody[key].properties[key2] = {}

              let tempPayload = payload[key]
              if (typeof tempPayload[key2] === 'object' && tempPayload[key2] !== null) {
                objBody[key].properties[key2].type = "object"
                objBody[key].properties[key2].properties = {}
                self.generateBody(objBody[key].properties[key2].properties, tempPayload[key2])
              } else {
                self.generateObject(objBody[key].properties, key2, tempPayload[key2])
              }

            }
          } else {
            objBody = self.generateObject(objBody, key, payload[key])
          }
        }
      }

      return objBody
    },

    generateObject(objBody, key, value) {

      objBody[key] = {}
      objBody[key].type = typeof value
      objBody[key].example = value

      objBody[key].description = key

      if (typeof value == "string") {
        objBody[key].maxLength = value.length
      } else if (typeof value == "number") {
        objBody[key].maximum = value.length
      }

      return objBody
    },
    generateArrayValue(objBody, key, value) {
      objBody.items = {}
      objBody.items.type = typeof value[0]
      objBody.example = value
      objBody.description = key

      return objBody
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.width-field {
  width: 50%;
}

.gap-bottom {
  padding-bottom: 1rem;
}
</style>
