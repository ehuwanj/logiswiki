```dataviewjs
const allowed = ["routing", "shipit", "security", "devops", "aws", "kafka"];
const pages = dv.pages('"wiki"')
  .where(
    p =>
      p.file &&
      p.file.path &&
      (
        p.file.path.startsWith("wiki/concepts/") ||
        p.file.path.startsWith("wiki/sources/") ||
        p.file.path.startsWith("wiki/syntheses/")
      ) &&
      p.tags &&
      Array.isArray(p.tags) &&
      p.tags.length > 0
  );

const byFirstTag = {};
for (const t of allowed) byFirstTag[t] = [];

const invalid = [];
const missing = [];

for (const p of pages) {
  const first = p.tags?.[0];
  if (!first) {
    missing.push(p);
    continue;
  }
  if (allowed.includes(first)) {
    byFirstTag[first].push(p);
  } else {
    invalid.push({ page: p, firstTag: first });
  }
}

dv.header(2, "Wiki By Primary Tag");
for (const tag of allowed) {
  const items = byFirstTag[tag].sort((a, b) => a.file.name.localeCompare(b.file.name));
  dv.header(3, `${tag} (${items.length})`);
  if (items.length === 0) {
    dv.paragraph("- No pages");
  } else {
    dv.list(items.map(p => p.file.link));
  }
}

dv.header(2, "Quality Checks");

dv.header(3, `Missing tags (${missing.length})`);
if (missing.length === 0) {
  dv.paragraph("- None");
} else {
  dv.list(missing.sort((a, b) => a.file.name.localeCompare(b.file.name)).map(p => p.file.link));
}

dv.header(3, `Invalid first tag (${invalid.length})`);
if (invalid.length === 0) {
  dv.paragraph("- None");
} else {
  dv.table(
    ["Page", "First Tag"],
    invalid
      .sort((a, b) => a.page.file.name.localeCompare(b.page.file.name))
      .map(x => [x.page.file.link, x.firstTag])
  );
}
```