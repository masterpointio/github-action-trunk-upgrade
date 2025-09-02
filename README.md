[![Banner][banner-image]](https://masterpoint.io/)

# github-action-trunk-upgrade

[![Release][release-badge]][latest-release]

💡 Learn more about Masterpoint [below](#who-we-are-𐦂𖨆𐀪𖠋).

## Purpose and Functionality

A reusable GitHub Action for automated [Trunk](https://trunk.io) upgrades with status check handling and auto-merge.

This action automates the process of keeping your Trunk configuration up-to-date by creating pull requests for upgrades and automatically merging them after status checks pass. It follows security best practices with a two-token setup and only waits for required status checks, avoiding unnecessary delays from optional checks.

## Usage

### Prerequisites

- GitHub repository with Trunk configuration
- Personal Access Token from a code owner or team member (required)
- GitHub App credentials (recommended for enhanced performance and security)
- Repository permissions: `contents: write` and `pull-requests: write`

### Step-by-Step Instructions

1. **Set up authentication secrets** in your repository:
   - `BOT_APP_ID` - GitHub App ID
   - `BOT_APP_PRIVATE_KEY` - GitHub App private key
   - `CODE_OWNER_PAT` - Personal Access Token from a code owner or team member
2. **Create workflow file** `.github/workflows/trunk-upgrade.yml`:

```yaml
name: Trunk Upgrade
on:
  schedule:
    - cron: 0 9 1 * * # Monthly on the 1st at 9am
  workflow_dispatch: {}

permissions: read-all

jobs:
  trunk-upgrade:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write
    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2
      - uses: masterpointio/github-action-trunk-upgrade@abc123def456789012345678901234567890abcd # v1.0.0
        with:
          app-id: ${{ secrets.BOT_APP_ID }}
          app-private-key: ${{ secrets.BOT_APP_PRIVATE_KEY }}
          github-token: ${{ secrets.CODE_OWNER_PAT }}
          reviewers: "@org/engineering"
```

## Inputs

| Input                    | Description                                              | Required | Default     |
| ------------------------ | -------------------------------------------------------- | -------- | ----------- |
| `github-token`           | GitHub token for operations                              | Yes      |             |
| `app-id`                 | GitHub App ID for bot authentication                     | No       |             |
| `app-private-key`        | GitHub App private key                                   | No       |             |
| `reviewers`              | Reviewers to assign (e.g., `@org/team` or `user1,user2`) | No       | `""`        |
| `prefix`                 | Prefix for commit messages and PR titles                 | No       | `"chore: "` |
| `merge-method`           | Merge method (`squash`, `merge`, `rebase`)               | No       | `"squash"`  |
| `check-timeout-minutes`  | Max time to wait for checks (minutes)                    | No       | `"30"`      |
| `check-interval-seconds` | Interval between check polls (seconds)                   | No       | `"30"`      |

## Outputs

| Output                | Description                                             |
| --------------------- | ------------------------------------------------------- |
| `pull-request-number` | The number of the created PR                            |
| `pull-request-url`    | The URL of the created PR                               |
| `merged`              | Whether the PR was successfully merged (`true`/`false`) |

## Authentication

**GitHub Token Only:**

```yaml
with:
  github-token: ${{ secrets.GITHUB_TOKEN }}
```

**Two-Token Setup (Recommended):**

```yaml
with:
  app-id: ${{ secrets.BOT_APP_ID }}
  app-private-key: ${{ secrets.BOT_APP_PRIVATE_KEY }}
  github-token: ${{ secrets.CODE_OWNER_PAT }}
```

**How the two-token setup works:**

1. **GitHub App Token (Primary)**: Generated from `app-id` + `app-private-key`
   - Used for PR creation via trunk-io action
   - Higher rate limits (5,000/hr vs 1,000/hr)
   - Clean bot attribution in commits
   - Scoped permissions (only what the app needs)

2. **Personal Access Token (Code Reviewer)**: `github-token` input
   - Used for merge operations to satisfy code owner requirements
   - Should be from a user who is a code owner or team member
   - Required for repositories with code owner review requirements
   - Bypasses the "can't approve own PR" limitation
   - Falls back if no App credentials provided

**Why Two-Token Setup is Recommended:**

Many repositories have branch protection rules requiring code owner reviews. When a GitHub App creates a PR, it cannot approve its own PR due to GitHub's security model. The two-token approach solves this by:

- **App creates the PR** → Clean bot attribution
- **Code owner PAT approves/merges** → Satisfies repository protection rules

**Token Selection Logic:**

- If App credentials provided → Use App token for PR creation, PAT for approval/merge
- If no App credentials → Use PAT for both operations (**Note**: This won't work if the repository has rulesets or branch protection rules requiring code owner reviews, since the same user/token cannot create and approve their own PR)

## Built By

Powered by the [Masterpoint team](https://masterpoint.io/who-we-are/) and driven forward by contributions from the community ❤️

[![Contributors][contributors-image]][contributors-url]

## Contribution Guidelines

Contributions are welcome and appreciated!

Found an issue or want to request a feature? [Open an issue][issues-url]

Want to fix a bug you found or add some functionality? Fork, clone, commit, push, and PR — we'll check it out.

## Who We Are 𐦂𖨆𐀪𖠋

Established in 2016, Masterpoint is a team of experienced software and platform engineers specializing in Infrastructure as Code (IaC). We provide expert guidance to organizations of all sizes, helping them leverage the latest IaC practices to accelerate their engineering teams.

### Our Mission

Our mission is to simplify cloud infrastructure so developers can innovate faster, safer, and with greater confidence. By open-sourcing tools and modules that we use internally, we aim to contribute back to the community, promoting consistency, quality, and security.

### Our Commitments

- 🌟 **Open Source**: We live and breathe open source, contributing to and maintaining hundreds of projects across multiple organizations.
- 🌎 **1% for the Planet**: Demonstrating our commitment to environmental sustainability, we are proud members of [1% for the Planet](https://www.onepercentfortheplanet.org), pledging to donate 1% of our annual sales to environmental nonprofits.
- 🇺🇦 **1% Towards Ukraine**: With team members and friends affected by the ongoing [Russo-Ukrainian war](https://en.wikipedia.org/wiki/Russo-Ukrainian_War), we donate 1% of our annual revenue to invasion relief efforts, supporting organizations providing aid to those in need. [Here's how you can help Ukraine with just a few clicks](https://masterpoint.io/updates/supporting-ukraine/).

## Connect With Us

We're active members of the community and are always publishing content, giving talks, and sharing our hard earned expertise. Here are a few ways you can see what we're up to:

[![LinkedIn][linkedin-badge]][linkedin-url] [![Newsletter][newsletter-badge]][newsletter-url] [![Blog][blog-badge]][blog-url] [![YouTube][youtube-badge]][youtube-url]

... and be sure to connect with our founder, [Matt Gowie](https://www.linkedin.com/in/gowiem/).

## License

[Apache License, Version 2.0][license-url].

[![Open Source Initiative][osi-image]][license-url]

Copyright © 2016-2025 [Masterpoint Consulting LLC](https://masterpoint.io/)

<!-- MARKDOWN LINKS & IMAGES -->

[banner-image]: https://masterpoint-public.s3.us-west-2.amazonaws.com/v2/standard-long-fullcolor.png
[license-url]: https://opensource.org/license/apache-2-0
[osi-image]: https://i0.wp.com/opensource.org/wp-content/uploads/2023/03/cropped-OSI-horizontal-large.png?fit=250%2C229&ssl=1
[linkedin-badge]: https://img.shields.io/badge/LinkedIn-Follow-0A66C2?style=for-the-badge&logoColor=white
[linkedin-url]: https://www.linkedin.com/company/masterpoint-consulting
[blog-badge]: https://img.shields.io/badge/Blog-IaC_Insights-55C1B4?style=for-the-badge&logoColor=white
[blog-url]: https://masterpoint.io/updates/
[newsletter-badge]: https://img.shields.io/badge/Newsletter-Subscribe-ECE295?style=for-the-badge&logoColor=222222
[newsletter-url]: https://newsletter.masterpoint.io/
[youtube-badge]: https://img.shields.io/badge/YouTube-Subscribe-D191BF?style=for-the-badge&logo=youtube&logoColor=white
[youtube-url]: https://www.youtube.com/channel/UCeeDaO2NREVlPy9Plqx-9JQ
[release-badge]: https://img.shields.io/github/v/release/masterpointio/github-action-trunk-upgrade?color=0E383A&label=Release&style=for-the-badge&logo=github&logoColor=white
[latest-release]: https://github.com/masterpointio/github-action-trunk-upgrade/releases/latest
[contributors-image]: https://contrib.rocks/image?repo=masterpointio/github-action-trunk-upgrade
[contributors-url]: https://github.com/masterpointio/github-action-trunk-upgrade/graphs/contributors
[issues-url]: https://github.com/masterpointio/github-action-trunk-upgrade/issues
