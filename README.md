# GitHub Action: Check TLS/SSL Rating

A GitHub Action that checks the TLS/SSL cipher strength rating of a domain using `nmap`. The action succeeds when the rating meets the required minimum (default: A), and fails otherwise.

## Features

- 🔒 Checks TLS/SSL cipher strength using `nmap --script ssl-enum-ciphers`
- ⏰ Runs automatically every night at 2 AM UTC
- 🔧 Configurable URLs and ports
- ✅ Succeeds on rating A (or specified minimum rating)
- ❌ Fails on ratings B, C, D, E, F, or T

## Usage

### As a Reusable Action

You can use this action in your own workflows:

```yaml
steps:
  - name: Check TLS/SSL Rating
    uses: Oppedijk/github-action-check-tls-ssl-rating@main
    with:
      url: 'your-domain.com'
      port: '443'
      required-rating: 'A'
```

### Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `url` | Domain to check (without https://) | Yes | - |
| `port` | Port to check | No | `443` |
| `required-rating` | Required minimum rating (A, B, C, D, E, F) | No | `A` |

### Outputs

| Output | Description |
|--------|-------------|
| `rating` | The SSL/TLS rating result |

## Nightly Scheduled Check

This repository includes a nightly workflow that automatically checks configured domains. The workflow runs at 2 AM UTC every night.

### Configuring URLs to Check

To configure which URLs are checked nightly:

1. Edit `.github/workflows/check-tls-ssl.yml`
2. Add or modify steps for each domain you want to check:

```yaml
- name: Check example.com
  uses: ./
  with:
    url: 'example.com'
    port: '443'
    required-rating: 'A'

- name: Check another-domain.com
  uses: ./
  with:
    url: 'another-domain.com'
    port: '443'
    required-rating: 'A'
```

### Manual Trigger

You can also trigger the workflow manually from the GitHub Actions tab using the "Run workflow" button.

## How It Works

The action:
1. Installs `nmap` if not already available
2. Runs: `nmap --script ssl-enum-ciphers -p 443 <url>`
3. Extracts the rating using: `grep "least strength: " | awk -F 'least strength: ' '{print $2}'`
4. Compares the result against the required rating
5. Succeeds if rating is A (or meets requirement), fails otherwise (B, C, D, E, F, T)

## Rating Scale

- **A**: Strong (recommended)
- **B**: Good
- **C**: Acceptable
- **D**: Weak
- **E**: Very Weak
- **F**: Failed
- **T**: Timeout/Error

## Example Output

```
Checking SSL/TLS rating for scan.homeqgo.nl:443
Running nmap scan...
SSL/TLS Rating: A
✓ Rating A meets requirement (A)
```

## License

See [LICENSE](LICENSE) file for details.