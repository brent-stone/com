# brent-stone.com
brent-stone.com source files made public to hopeful help others with boilerplate
and design patterns for leveraging [mkdocs-material](https://squidfunk.github.io/mkdocs-material/plugins/blog/) and
[Cloudflare Pages](https://pages.cloudflare.com/) as a blog tech stack.

### Poetry
The [Poetry](https://python-poetry.org/) python project and virtual environment manager is recommended.

```bash
poetry env activate
# Copy-paste the command printed
poetry install
```

Ensure the `requirements.txt` is updated each time new dependencies are added.
```bash
poetry export --format requirements.txt --output requirements.txt --without-hashes
```
