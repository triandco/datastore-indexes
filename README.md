# Datastore indexes not found
This app is a Verida datastore using a custom schema and some pre-defined indexes:
* The app Create button creates an entry in the datastore every time it is clicked.
* The app Load button fetches two entries at a time from the datastore and displays them in a list.
* At the bottom is the content of the custom schema.

## Reproduction
1. Install the dependencies
```bash
npm install
```
2. Star development server:
```bash
npm run dev
```
3. Navigate to [localhost:8080](http://localhost:8080). You should see your app running. 
4. Enter with Metamask
5. Click the add button a couple of times to create a few entries in the datastore.
6. Click the load button to fetch entries from the datastore

## Expectation
Fetch result contains bookmark for pagination

## Actual
Fetch result does not have a bookmark property but contains a warning that no indexes found.