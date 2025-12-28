## How to use it

### Preview as you write (default behavior)

This mirrors the `docker run -p 8000:8000 ...` command.

```bash
docker compose up
````

Then open:

```
http://localhost:8000
```

Live reload works because your current directory is mounted into `/docs`.

---

### Build the static site

This mirrors `docker run ... zensical/zensical build`.

```bash
docker compose run --rm zensical build
```

The generated static site will appear in the output directory created by Zensical inside your project folder.

---

### Optional: split preview vs build (if you prefer clarity)

If you want explicit services instead of overloading one:

```yaml
version: "3.9"

services:
  zensical-preview:
    image: zensical/zensical
    ports:
      - "8000:8000"
    volumes:
      - .:/docs

  zensical-build:
    image: zensical/zensical
    volumes:
      - .:/docs
    command: ["build"]
```

Run preview:

```bash
docker compose up zensical-preview
```

Run build:

```bash
docker compose run --rm zensical-build
```