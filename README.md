# DSN to Struct Parser for Golang

A lightweight, declarative Go library to parse DSN (Data Source Name) strings directly into structured Go types using struct tags.

## ✨ What is This Project?

This library allows you to define a Go struct with a special `dsn` tag and automatically populate its fields by parsing a DSN string. It supports dynamic path extraction, optional fields, default values, and query parameters.

Instead of writing custom parsers for different schemes (like `s3`, `ftp`, `http`, `fs`), you describe the expected format once and the parser handles the rest.

## ❓ Why is This Needed?

Many Go applications use DSNs to configure remote storage, databases, file systems, and external APIs. Typically, each DSN format requires custom parsing logic. This results in duplicated code, increased complexity, and reduced maintainability.

This library solves that by:
- Letting you define the DSN structure declaratively
- Automatically parsing DSNs into Go structs
- Supporting advanced patterns like embedded credentials, custom ports, query parameters, and defaults

## ✅ Supported Features

- DSN pattern parsing via struct tags (e.g., `dsn:"s3://$AccessKey:$SecretKey@$Endpoint/$Bucket"`)
- Query parameter mapping to `map[string]string`
- Default value support using `default:"..."` tags
---

## 📦 Installation

```bash
go get github.com/getevo/dsn
```

---

## 🔍 Examples

### HTTP Example

```go
type HTTP struct {
	DSN    string `dsn:"http(s)://$Path"`
	Scheme string
	Path   string
	Debug  bool `default:"false"`
	Params map[string]string
}
```

**Usage:**

```go
dsn := "https://upload.wikimedia.org/wikipedia/commons?header[Authorization]=Bearer TOKEN"
var cfg HTTP
err := ParseDSN(dsn, &cfg)
```

**Result:**

```go
cfg = HTTP{
	Scheme: "https",
	Path:   "upload.wikimedia.org/wikipedia/commons",
	Debug:  false,
	Params: map[string]string{
		"header[Authorization]": "Bearer TOKEN",
	},
}
```

---

### File System (fs)

```go
type FileSystem struct {
	DSN    string `dsn:"fs://$Path"`
	Scheme string
	Path   string
	Debug  bool `default:"false"`
	Params map[string]string
}
```

**Input:**

```go
"fs:///mnt/storage/data?type=SSD"
```

**Result:**

```go
FileSystem{
	Scheme: "fs",
	Path:   "/mnt/storage/data",
	Debug:  false,
	Params: map[string]string{"type": "SSD"},
}
```

---

### Amazon S3

```go
type S3 struct {
	DSN       string `dsn:"s3://$AccessKey:$SecretKey@$Endpoint/$Bucket"`
	Scheme    string
	Region    string
	Endpoint  string
	AccessKey string
	SecretKey string
	Bucket    string
	BasePath  string `default:""`
	Debug     bool   `default:"false"`
	IgnoreSSL bool   `default:"false"`
	Params    map[string]string
}
```

**Input:**

```go
"s3://key:secret@https://s3.amazonaws.com/mybucket?Region=us-east-1&BasePath=data/files"
```

---

### FTP / SFTP

```go
type FTP struct {
	DSN      string `dsn:"ftp://$Username:$Password@$Host:$Port/$BasePath"`
	Scheme   string
	Username string
	Password string
	Host     string
	Port     int    `default:"21"`
	BasePath string `default:""`
	Debug    bool   `default:"false"`
	Params   map[string]string
}
```

**Input:**

```go
"ftp://user:pass@example.com:21/data?Debug=true"
```

---

## 🧪 Testing

A full set of test cases is included and covers:

- Default values
- Param parsing
- Type mapping (string, int, bool)
- Field-to-pattern matching

Run with:

```bash
go test ./...
```

---

---

## 📄 License

MIT — feel free to use, modify, and contribute.

---

Let me know if you want a logo, GoDoc badge, or GitHub Actions integration for this project!
