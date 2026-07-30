# {{ .title }}

{{ if .featured }}⭐ **Featured Post**{{ end }}

| | |
|---|---|
| **Date** | {{ .date }} |
| **Difficulty** | {{ .difficulty }} |
{{ if .reading_time }}| **Reading time** | {{ .reading_time }} min |
{{ end }}{{ if .author }}| **Author** | {{ .author }} |
{{ end }}{{ if .language }}| **Language** | {{ .language }} |
{{ end }}{{ if .version }}| **Version** | {{ .version }} |
{{ end }}
{{ if .prerequisites }}
## Prerequisites

{{ range .prerequisites }}- {{ . }}
{{ end }}{{ end }}
{{ .body }}

{{ if .tags }}
**Tags:** {{ range .tags }}`{{ . }}` {{ end }}
{{ end }}
{{ if .references }}
## References

{{ range $label, $url := .references }} - [{{ $label }}]({{ $url }})
{{ end }}{{ end }}
