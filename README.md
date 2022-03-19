# SPARQL-Validator

`SPARQL-Validator` is a GitHub Action that checks whether the SPARQL queries included in one or multiple `.sparql` files are well formed. The action evaluates each of the queries found in the repository in the SPARQL endpoint specified as a parameter. If the status obtained after evaluating this query is `400`, this means that the file is not well formed and the action will fail. Independently of the result of the execution, the action will put a comment in the pull request with the results of the execution.

## Usage
Create a `.github.workflows/[name].yaml` file in the repository.

Example workflow:
```
name: [name]
on:   
  pull_request:
    branches: [master]

jobs:    
  [name]:
    runs-on: ubuntu-latest
    name: [name]
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      
      - name: github-action-sparql
        uses: oeg-upm/github-action-sparql@v1.0.0
        id: 'github-action-sparql'
        with:
          owner: ${{ github.repository_owner }}
          repo: ${{ github.event.repository.name }}
          pr_number: ${{ github.event.number }}
          token: ${{ secrets.GITHUB_TOKEN }}
          actor: ${{ github.actor }}
          endpoint: '[endpoint]'
          graph_uri: [uri]
          format: [format]
          path: [path]

      - name: update files and push to branch
        if: always()
        run: |
          git config --global user.name "github-actions[bot]"
          git config --global user.email "41898282+github-actions[bot]@users.noreply.github.com"

          git add -A
          git commit -m "Output ${{ github.actor }} - ${{ github.event.number }}" --allow-empty
          git push origin HEAD:${{ github.head_ref }} --force
```
## Inputs
### `owner`
The owner of the repository, it is taken from `${{ github.repository_owner }}`. 
### `repo`
The repository name, it is taken from `${{ github.event.repository.name }}`. 
### `pr_number`
The pull request number, it is taken from `${{ github.event.number }}`. 
### `token`
The account access token, it is taken from `${{ secrets.GITHUB_TOKEN }}`. 
### `actor`
The account that created the pull request, it is taken from `${{ github.actor }}`. 
### `endpoint`
The endpoint for the SPARQL queries, without `https://`.
### `graph_uri`(optional)
The graph_uri for the SPARQL query. 
### `format`(optional)
The format of the output of the query.

| Option | Format |
| :----------- | :----------- |
| `default` | `html` |
| `application/json` | `json` |
| `application/javascript` | `javascript` |
| `application/turtle` | `turtle` |
| `text/plain` | `N-Triplets` |
| `CSV` | `csv` |
| `Spreadsheet` | `html` |
| `XML` | `xml` |
| `RDF` | `rdf` |
### `path`(optional)
The `default` path is the path where the files with the querys are stored.

`None` indicates that the results are not stored in the repository.

If the path in the parameter is set, it should look like: `[name]/[name]`

The path to store the outputs of the querys will look like, `[path]/[filename]-[actor].[output]`.
