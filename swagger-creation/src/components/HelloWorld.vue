<template>
  <div class="hello">
    <div>
      <span>API Title</span>
      <input v-model="title" type="text">
    </div>
    <div>
      <span>API path</span>
      <input v-model="path" type="text">
    </div>
    <div>
      <span>API tags</span>
      <input v-model="tag" type="text">
    </div>
    <div>
      <span>Description</span>
      <textarea v-model="description" rows="9" cols="50"></textarea>
    </div>
    <div>
      <span>Request body json</span>
      <textarea v-model="requestBody" rows="9" cols="50"></textarea>
    </div>
    <div>
      <span>Response body json</span>
      <textarea v-model="responseBody" rows="9" cols="50"></textarea>
    </div>

    <br>
    <!-- <div>
      <span>Generated swagger</span>
      <textarea v-model="swaggerFile" rows="4" cols="50"></textarea>
    </div> -->
    <button @click="generateSwagger()">Generate Swagger</button>

    <br>

    <div>
      <span>Generated swagger file</span>
      <textarea :value="changeJsonToString(swaggerFile)" id="" cols="50" rows="10" readonly></textarea>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data() {
    return {
      title: "Corporate - Account Balance Inquiry",
      path: "/api/my/crp/v1/accountbalinq",
      tag: "Account Balance Inquiry",
      httpMethod: "post",
      description: "Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum.",
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
  methods: {
    changeJsonToString(payload){
      return JSON.stringify(payload)
    },  
    generateSwagger() {
      const self = this

      self.swaggerFile.info.title = self.title
      self.swaggerFile.info.description = self.description
      let jsonReqBody = JSON.parse(self.requestBody)
      let jsonResBody = JSON.parse(self.responseBody)

      // let jsonReqBody = {
      //   "corporateId": "MYMAYBANK",
      //   "accountNo": "012345678910",
      //   "accountCCY": "USD",
      //   "accountType": "Savings",
      //   "provider": "MY",
      //   "messageId": "APIGWYYYYMMDDHHMMSSXXXXXX",
      //   "informations": [
      //     {
      //       "currency": "MYR",
      //       "value": 10,
      //       "sharingNumber": [1, 2, 3]
      //     }
      //   ]
      // }
      // let jsonResBody = {
      //   "accountName": "ARISSA BAKERY",
      //   "accountCCY": "USD",
      //   "availBal": 100.1,
      //   "availBalSign": "C",
      //   "ledgerBal": 100.1,
      //   "ledgerBalSign": "C",
      //   "currentBal": 100.1,
      //   "currentBalSign": "C",
      //   "ownBal": 100.1,
      //   "ownBalSign": "C",
      //   "holdAmt": 100.1,
      //   "facilityLimit": 100.1,
      //   "odAmt": 100.1,
      //   "intraOd": 100.1,
      //   "floatOne": 100.1,
      //   "floatTwo": 100.1,
      //   "ocLateFloat": 100.1,
      //   "totalFloat": 100.1,
      //   "utilisedAmt": 100.1,
      //   "unutilisedAmt": 100.1
      // }

      self.generatedReqBody = self.generateBody(self.swaggertRequestBody.content["application/json"].schema.properties, jsonReqBody)
      self.generatedResBody = self.generateBody(self.swaggertResponseBody["200"].content["application/json"].schema.properties, jsonResBody)

      console.log("generatedReqBody", self.generatedReqBody);
      console.log("generatedResBody", self.generatedResBody);
      self.swaggertRequestBody.content["application/json"].schema.properties = self.generatedReqBody
      self.swaggertResponseBody["200"].content["application/json"].schema.properties = self.generatedResBody
      self.swaggertResponseBody["200"].headers = self.headerResParameter
      
      // console.warn("lalala 1", self.swaggertRequestBody.content["application/json"].schema.properties);
      // console.warn("lalala 2", self.swaggertResponseBody["200"].content["application/json"].schema.properties);

      self.swaggerFile.paths[self.path] = {}
      self.swaggerFile.paths[self.path][self.httpMethod] = {
        tags: [self.tag],
        operationId: "",
        responses: self.swaggertResponseBody,
        // parameters: self.headerReqParameter,
        requestBody: self.swaggertRequestBody
      }

      console.warn(self.swaggerFile);
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
          objBody = self.generateObject(objBody, key, payload[key])
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
<style scoped></style>
