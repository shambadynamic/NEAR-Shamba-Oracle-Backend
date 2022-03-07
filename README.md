After the user enters all the details, and submits the form, this is the workflow:

    1. The form-submit calls an API with the request body as the form details that the user has entered, in the format as shown below:

        Example of that API call is:

            URL: http://165.232.134.108:4000/

            Method: POST

            REQUEST Body:

            {
                "searchValue": "agg_max",
                "datasetCodeValue": "COPERNICUS/S2_SR",
                "selectedBandValue": "NDVI",
                "startDateValue": "2021-09-01",
                "endDateValue": "2021-09-10",
                "imageScaleValue": 250,
                "geometryValue": {"type": "FeatureCollection", "features": [{ "type": "Feature", "properties": {}, "geometry": { "type": "Polygon", "coordinates": [[[19.51171875, 4.214943141390651], [18.28125, -4.740675384778361], [26.894531249999996, -4.565473550710278], [27.24609375, 1.2303741774326145], [19.51171875, 4.214943141390651]]]}}]}
            }


            RESPONSE Body:

            {
                "result_type": "agg_max",
                "result": "0.924997091293335",
                "transactions": [
                    "https://explorer.testnet.near.org/transactions/2yuzAgXhT6SzgeA7Pbea47Tf88BfaTMoC2EMjwXD1hbU",
                    "https://explorer.testnet.near.org/transactions/tMvtmZifqkafzRYhAjgVsjGi4xcRS4Li7aQNbRbcVeo"
                ]
            }

    2. Then, that API initiates a call to the "Client" NEAR contract requesting the "agg_value" (in the example shown above, it's "agg_max"). The Client Contract has an existing balance of 50 fungible tokens (FT) that it can access to pay for requests. 

    3. With the above request being successful, the user gets a transaction url as the first transaction (in the above example, it's https://explorer.testnet.near.org/transactions/2yuzAgXhT6SzgeA7Pbea47Tf88BfaTMoC2EMjwXD1hbU i.e., the first element of the "transactions" array in the RESPONSE body of the API).

    4. Along with that, 10 fungible tokens are sent to the on-chain Oracle Contract along with the user's request.

    5. Then, the Oracle-Node that is being created using Chainlink, finds requests by consistently polling the on-chain Oracle Contract. When a new request is found, the Oracle-Node begins processing the request. The FT payment is locked until the Client Contract receives a successfully completed request.

    6. With the original request in hand, the off-chain oracle node interfaces with the Shamba GeoAPI and retrieves the requested "agg_value" (in the example shown above, it's "agg_max").

    7. The off-chain Oracle-Node passes the "agg_value" (i.e., "agg_max" in the above example) from the API to the on-chain Oracle Contract.

    8. Hence, the on-chain Oracle Contract fulfills the original request by providing the "agg_value" (i.e., "agg_max" in the above example) from the API to the Client Contract. 

    9. With this fulfilled request, the initial FT payment is now unlocked and can be accessed by the owner of the Oracle Contract / Oracle Node. Both the on-chain Oracle Contract and off-chain Oracle Node are typically owned by the same party.

    10. Again, the user gets another transaction url as the second transaction (in the above example, it's https://explorer.testnet.near.org/transactions/tMvtmZifqkafzRYhAjgVsjGi4xcRS4Li7aQNbRbcVeo i.e., the second element of the "transactions" array in the RESPONSE body of the API).

    11. Finally, the requested "agg_value" (i.e., "agg_max" in the above example) is now rendered to the front-end as a pop-up alert box, and both the transaction urls are shown in the "Transactions" tab.
