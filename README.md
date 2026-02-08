# DB Operator Documentation

## How to develop

### With poetry

1. Install poetry: <https://python-poetry.org/docs>
2. Run `poetry install --no-root`
3. Run `poetry run mkdocs serve`

### Pre commit hook

It's not required to use the `pre-commit` hooks, cause they will run anyway during `CI`, but if you want to see the issues before pushing, it's recommended to set it up. Please find more info here: <https://pre-commit.com/>
