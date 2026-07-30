# {{ .title }}
{{- if .featured }}

⭐ **Featured Post**
{{- end }}
{{- if .status }}

📌 **Status: {{ .status }}**
{{- end }}
{{- if .cover_image }}

![Cover]({{ .cover_image }})
{{- end }}

| | |
|---|---|
| **Date** | {{ .date }} |
{{ if .last_updated }}| **Last updated** | {{ .last_updated }} |
{{ end }}{{ if .category }}| **Category** | {{ .category }} |
{{ end }}| **Difficulty** | {{ .difficulty }} |
{{ if .reading_time }}| **Reading time** | {{ .reading_time }} min |
{{ end }}{{ if .word_count }}| **Word count** | ~{{ .word_count }} |
{{ end }}{{ if .author }}| **Author** | {{ .author }} |
{{ end }}{{ if .reviewers }}| **Reviewers** | {{ range .reviewers }}{{ . }}, {{ end }} |
{{ end }}{{ if .language }}| **Language** | {{ .language }} |
{{ end }}{{ if .version }}| **Version** | {{ .version }} |
{{ end }}{{ if .license }}| **License** | {{ .license }} |
{{ end }}{{ if .series }}| **Series** | {{ .series }}{{ if .series_order }} (Part {{ .series_order }}){{ end }} |
{{ end }}
{{- if .canonical_url }}

*Originally published at: [{{ .canonical_url }}]({{ .canonical_url }})*
{{- end }}
{{- if .github_url }}

🔗 **Source code:** [{{ .github_url }}]({{ .github_url }})
{{- end }}
{{- if .prerequisites }}

## Prerequisites

{{ range .prerequisites }}- {{ . }}
{{ end }}
{{- end }}
{{- if .introduction }}

## Overview

{{ .introduction }}
{{- end }}
{{- if .basic_usage }}

## Basic Usage

{{ .basic_usage }}
{{- end }}
{{- if .examples }}

## Examples

{{ .examples }}
{{- end }}
{{- if .best_practices }}

## Best Practices

{{ .best_practices }}
{{- end }}
{{- if .pitfalls }}

## Common Pitfalls

{{ .pitfalls }}
{{- end }}
{{- if .advanced }}

## Advanced Topics

{{ .advanced }}
{{- end }}
{{- if .summary }}

## Summary

{{ .summary }}
{{- end }}
{{- if .body }}

{{ .body }}
{{- end }}
{{- if .related_posts }}

## Related Posts

{{ range .related_posts }}- {{ . }}
{{ end }}
{{- end }}
{{- if .tags }}

**Tags:** {{ range .tags }}`{{ . }}` {{ end }}
{{- end }}
{{- if .references }}

## References

{{ range $label, $url := .references }} - [{{ $label }}]({{ $url }})
{{ end }}
{{- end }}
