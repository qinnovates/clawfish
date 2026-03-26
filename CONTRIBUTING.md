# Contributing to ClawFish

Thank you for your interest in contributing to the ClawFish SEO Resource Library! We welcome contributions from the community to keep this list comprehensive and up-to-date.

## How to Contribute

### Suggesting a Resource

The easiest way to contribute is to [open an issue](../../issues/new?template=suggest-resource.yml) using our resource suggestion template.

### Submitting a Pull Request

1. **Fork** this repository
2. **Create a branch** for your addition: `git checkout -b add-resource-name`
3. **Add your resource** to the appropriate section in `README.md`
4. **Update** `data/resources.json` with the machine-readable entry
5. **Submit a pull request** with a clear description

### Guidelines for Resources

#### What We Accept
- Open-source SEO tools and libraries
- Commercial SEO tools and platforms (must be established and reputable)
- Educational content (courses, books, tutorials, guides)
- Community resources (forums, Slack groups, Discord servers)
- SEO APIs and datasets
- Browser extensions
- Newsletters, blogs, and podcasts focused on SEO
- Research papers and case studies

#### What We Do Not Accept
- Spam or low-quality link submissions
- Resources behind aggressive paywalls with no free tier
- Duplicate entries (check the list first)
- Resources that are outdated or no longer maintained (for tools/repos)
- Self-promotional content without genuine value

### Formatting

Each entry should follow this format:

```markdown
- **[Resource Name](URL)** — Brief, accurate description of the resource.
```

For GitHub repositories, include star counts when known:

```markdown
- **[Repo Name](URL)** — Description. ![GitHub stars](https://img.shields.io/github/stars/owner/repo)
```

### JSON Entry Format

When adding to `data/resources.json`, use this structure:

```json
{
  "name": "Resource Name",
  "url": "https://example.com",
  "category": "Category Name",
  "tags": ["tag1", "tag2"],
  "type": "repo|tool|article|course|community|api|dataset|extension|book|podcast|newsletter|blog"
}
```

### Quality Standards

- **Accuracy**: Descriptions must be factual and concise
- **URLs**: All links must be working and point to the correct resource
- **Categories**: Place resources in the most appropriate category
- **No duplicates**: Search the list before adding

### Reporting Issues

- **Broken links**: Open an issue with the `broken-link` label
- **Incorrect information**: Open an issue or submit a PR with corrections
- **Category suggestions**: Open an issue to discuss new categories

## Code of Conduct

Be respectful, constructive, and professional in all interactions. We are building a resource for the entire SEO community.

## License

By contributing, you agree that your contributions will be licensed under the [MIT License](LICENSE).
