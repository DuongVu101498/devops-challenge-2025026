# Solution:
```bash
jq -r 'select(.symbol == "TSLA" and .side == "sell") | .order_id' ./transaction-log.txt | xargs -I {} curl -s "https://example.com/api/{}" >> ./output.txt
```
# Explanation:

1)
 ```bash
jq -r 'select(.symbol == "TSLA" and .side == "sell") | .order_id' ./transaction-log.txt
 ```
Filters the JSON entries where symbol is "TSLA" and side is "sell", then extracts the order_id.

2)
```bash
xargs -I {} curl -s "https://example.com/api/{}"
 ```
Substitutes {} with each extracted order_id and sends a GET request using curl.

3)
```bash
>> ./output.txt
 ```
Appends the HTTP response to output.txt.

This will submit GET requests for all TSLA sell orders and save the results in output.txt

**Reference: Chat GPT**
