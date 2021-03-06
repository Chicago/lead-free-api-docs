# API Documentation

## Submitting Patient Record

### Authentication

Submitting patient records to Lead Safe requires client credentials to retrieve a token, which is required for all API calls. City of Chicago will issue a client ID and a client secret ID which will be used to obtain a token.


```bash
GET --user CLIENT_ID:CLIENT_SECRET --data "grant_type=client_credentials" <url>/oauth/token
```

Successful requests for a token will return a JSON file similar to this:

```json
{"access_token":"TOKEN","token_type":"bearer","expires_in":3600}
```

Tokens are set to expire every 3,600 seconds (1 hour) and a new token will need to be obtained.

To upload a patient record, the POST command must include a header called `Authorization: Bearer` followed by the TOKEN obtained in the previous steps.

An upload would look like:

```bash
POST --header "Content-Type:application/json" --header "Authorization: Bearer TOKEN" FILE.JSON <url>/upload/insert/
```

The format for `FILE.JSON` is described in the next section.

### Character Limit

There is a 20,000 character limit for submissions.

### Schema
 
Submit a patient record and retrieve the estimated risk of having elevated blood-lead levels.


| Field              | Format   | Constraints                        | Notes                                                                                                             |
|--------------------|----------|------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| timestamp          | TimeDate | yyyy-mm-dd hh:mm:ss.sss-hh:mm      | Current timestamp (RFC 3339 compliant date)                                                                       |
| network_id         | Text     | <N/A\>                             | The parent entity of the submitting organization                                                                  |
| clinic_id          | Text     | <N/A\>                             | Organization ID                                                                                                   |
| location_id        | Text     | <N/A\>                             | Specific Location Abbreviation in Centricity                                                                      |
| patient_id         | Text     | <N/A\>                             | AlphaNumeric patient identifier                                                                                   |
| address1           | Text     | <N/A\>                             | Patient home address (street number, street direction, street name, street type)                                  |
| address2           | Text     | <N/A\>                             | Patient home address (additional information, e.g. apartment number)                                              |
| city               | Text     | <N/A\>                             | Patient home address city                                                                                         |
| state              | Text     | <N/A\>                             | Patient home address state                                                                                        |
| zip                | Text     | <N/A\>                             | Patient home address zip code                                                                                     |
| date_of_birth      | Date     | yyyy-mm-dd                         | Patient date of birth                                                                                             |
| expected_due_date  | Date     | yyyy-mm-dd                         | _Optional_ Expected due date, if applicable                                                                        |
| gender             | Text     | M/F/U                              | Patient gender                                                                                                    |
| race               | Text     | Code Values from Centricity        | Standard ONC Race definitions <br /> <br /> **Code / description** <br /> 1002-5 / American Indian or Alaska Native <br /> 2028-9 / Asian <br /> 2054-5 / Black or African American <br /> 2076-8 / Native Hawaiian or Other Pacific Islander <br /> 2106-3 / White <br /> UNK / Unknown |
| ethnicity          | Text     | Code Values from Centricity        | Standard ONC Ethnicity definitions <br/> <br/> **Code / description** <br /> 2135-2 / Hispanic or Latino <br /> 2186-5 / Non-Hispanic or Latino <br /> UNK / Unknown |
| VISIT ARRAY        |          |                                    | _Optional_ May entirely omit the array if visit history does not exist or is unavailable.  |
| visit.visit_id     | Numeric  | 16 Digit GE ID                     | Unique GE ID for specific visit (DocumentID)                                                                      |
| visit.date         | Date     | yyyy-mm-dd hh:mm:ss.sss-hh:mm      | Date of visit (RFC 3339 complient date)                                                                           |
| visit.location     | Text     | <N/A\>                             | Text Abbreviation for facility of visit location                                                                  |
| visit.provider     | Text     | <N/A\>                             | Full provider name, last name, or just NPI                                                                        |
| LAB ARRAY          |          |                                    | _Optional_ May entirely omit the array if visit history does not exist or is unavailable.  |
| lab.lab_id         | Numeric  | 16 Digit GE ID                     | Unique GE ID for specific lab result (ObsID)                                                                      |
| lab.type           | Text     | "BLL"                              | Static BLL unless we identify additional labs to include                                                          |
| lab.date           | Date     | yyyy-mm-dd                         | Date of lab results                                                                                               |
| lab.sample_type    | Text     | V/C                                | "V" Venous <br /> "C" for Capillary                                        |
| lab.result         | Text     | <N/A\>                             | Several values are possible; integer, non-integer numeric, ranges indicated by alphanumeric text.                 |

```json
{
  "timestamp": "2018-03-23 09:59:14.237-00:00", 
  "network_id": "Alliance Health",
  "clinic_id": "EF",
  "location_id": "examp_loc",
  "patient_id": "9000", 
  "address1": "333 S State St", 
  "address2": "Ste 420", 
  "city": "Chicago", 
  "state": "IL", 
  "zip": "60653", 
  "date_of_birth": "1988-03-17", 
  "expected_due_date": "2018-07-24", 
  "gender": "F", 
  "race": "2054-5", 
  "ethnicity": "2186-5",
  "visit": [
    {"visit_id": 1234567891011121, "date": "2017-07-25 15:02:54.171-00:00", "location": "333 S State St", "provider": "John Doe"},
    {"visit_id": 1234567891011121, "date": "2017-07-25 15:02:54.171-00:00", "location": "333 S State St", "provider": "John Doe"}, 
    {"visit_id": 1234567891011121, "date": "2017-07-25 15:02:54.171-00:00", "location": "333 S State St", "provider": "John Doe"}
  ],
  "lab": [
    {"lab_id": 1234567891011121, "type": "BLL", "date": "2016-07-29", "sample_type": "V", "result": "None Detected ug/dL"}, 
    {"lab_id": 1234567891011121, "type": "BLL", "date": "2015-08-21", "sample_type": "V", "result": "16.3"}, 
    {"lab_id": 1234567891011121, "type": "BLL", "date": "2015-02-27", "sample_type": "V", "result": "1"}] 
}
```

### Response

#### Body

| Field            | Format   | Constraints                   | Notes/Questions                                                                            |
|------------------|----------|-------------------------------|--------------------------------------------------------------------------------------------|
| version          | Text     | "1.0.0-rc.2"                  | Follows Semantic Versioning 2.0.0 http://semver.org/spec/v2.0.0.html                       |
| timestamp        | TimeDate | yyyy-mm-dd hh:mm:ss.sss-hh:mm | Current Timestamp (RFC 3339 compliant date).                                               |
| network_id       | Text     | <N/A\>                        | Parent entity - same value as submitted to the API.                                        |
| clinic_id        | Text     | <N/A\>                        | Organization ID - same value as submitted to the API.                                      |
| location_id      | Text     | <N/A\>                        | Specific Location Abbreviation in Centricity - same value as submitted to the API.         |
| patient_id       | Text     | AlphaNumeric                  | Identifier for the patient - same value as submitted to the API.                           |
| risk_score       | Text     | 9.99                          | Currently expecting numeric, future may be phrased by stating an odds ratio with the risk score. |
| risk_score_notes | Text     | <N/A\>                        | Notes and instructions for clinical staff based on `risk_score`. Notes with multiple lines will be semi-colon (;) delimited. |

```json
{
    "version": "1.0.0-rc.2", 
    "timestamp": "2017-08-11 19:20:29.000-00:00", 
    "network_id": "Alliance Health",
    "clinic_id": "EF",
    "location_id": "examp_loc",
    "patient_id": "9000", 
    "risk_score": "0.309", 
    "risk_score_notes": "Risk Score Notes"
}
```

#### Response Codes

The API will return codes to indicate whether the prediction encountered any errors. Often, error codes will be accompanied by a longer explanation of the error in addition to the brief explanations below.

|        Status Class         | Status Code  |                                    Status Message                                            |
|-----------------------------|:------------:|----------------------------------------------------------------------------------------------|
| Success                     | 200          | No error.                                                                                    |
| Missing data field          | 400          | A required field was not included in the submission.                                         |
| Incorrect data type         | 400          | A field contained an unexpected data type that does not match submission requirements.       |
| Incorrect date format       | 400          | One of the date formats is incorrect.                                                        |
| Invalid JSON                | 400          | JSON structure was incomplete, contained unexpected characters, or was not properly closed.  |
| Invalid race/ethnicity code | 400          | Value used to encode race or ethnicity does not match an expected value.                     |
| Unauthorized                | 401          | Failed to provide authentication key or provided invalid key.                                |
| Not Found                   | 404          | The URI requested is invalid or does not exist.                                              |
| Child over 1 years-old      | 412          | The child is ineligible for Lead Safe.                                                       |
| Address outside of Chicago  | 412          | Address is outside of Chicago or address failed to be geocoded within Chicago.               |

```json
"errors":
  [
    {
      "message": "An error message which conveys details",
      "code": 400
    }
  ]
```

#### Header

| Field            | Format       | Constraints      | Notes                                                                                       |
|------------------|--------------|------------------|---------------------------------------------------------------------------------------------|
| Date             | Integer      | <N/A\>           | ID of the record submitted.                                                                 |
| Server           | Text         | "Apache"         | The type of the server providing the results                                                |
| Content-Location | URL          | <N/A\>           | Permanent location to retrieve the results                                                  |
| ETag             |              | <N/A\>           |                                                                                             |
| Content-Length   | Integer      | 225              |                                                                                             |
| Content-Type     | Text         | application/json | Informs user that the content will be a JSON file                                           |
| Set-Cookie       | Text         | <N/A\>           |                                                                                             |

```
HTTP/1.1 200 OK
Date: Tue, 22 Aug 2017 22:49:39 GMT
Server: Apache
Content-Location: https://webapps1int.cityofchicago.org/ords/cdph_lead_api/test_result/get/406
ETag: "zi/wQ5GvoQFjvb4ej24v8f5PbHog14ZsjDSGJABXUxKxV3SxyOwBKrNJv7we9B5hnSs9WgeoA2h54/yVmIjcnA=="
Content-Length: 225
Content-Type: application/json
Set-Cookie: BIGipServerwebapps1int.cityofchicago.org-443.app~webapps1int.cityofchicago.org-443_pool=339105802.47873.0000; Secure; HttpOnly; path=/; Httponly; Secure
```


## Retrieving Previous Predictions

When submitting a record for a prediction, a unique URL is given to the submission (not the individual) where the results can be retrieved in the future. 

```bash
GET <url>/get/{id}
```

The response will be the [same as the original prediction](#body).

!!! note ""
    The prediction is only assigned when it is POST to the server. The score will only reflect the prediction given the data originally submitted and based on known information at that time. Retrieving old records will not update the prediction. To get a new prediction, submit a new record


## Checking Status on All Submissions

Check the status on all submissions and whether results are available.

```bash
GET <url>/get/
```

### Response

| Field       | Format       | Constraints    | Notes                                                                                            |
|-------------|--------------|----------------|--------------------------------------------------------------------------------------------------|
| id          | Integer      | <N/A\>         | ID of the record submitted.                                                                      |
| processed   | Text         | "Y" or "N"     | Whether the results are available. Results may be an error or blank if the record was incorrect. |

```json
{"items":[
  {"id":133,"processed":"Y"},
  {"id":121,"processed":"Y"},
  {"id":120,"processed":"Y"},
  {"id":122,"processed":"Y"},
  {"id":123,"processed":"Y"}
  ]
}
```


## Interpreting Risk Levels

Elevated lead levels have severe impacts on a child's mental and physical development. When the API identifies elevated blood-lead levels, doctors are highly encouraged to conduct further blood tests

!!! info "Follow-up after the blood test"
	If elevated lead levels are confirmed by subsequent blood testing, the family has multiple options when deciding how to mitigate potential sources of lead poisoning: The City of Chicago has [limited grant funding](https://www.cityofchicago.org/city/en/depts/cdph/provdrs/healthy_homes/svcs/apply_for_financialassistanceforleadabatement.html) to assist low-income homeowners and landlords of affordable housing to eliminate lead hazards. Families who rent their home from a landlord can also contact the City of Chicago for inspectors to inspect their homes for potential sources of lead poisoning. Families may dial 311 for further assistance or contact CDPH at 312-747-LEAD(5323).
