# Project Overview

This is an **Apache JMeter** test plan project for performance testing **The Movie DB (TMDB) API**. It contains a single JMeter test plan file (`.jmx`) configured to load test various TMDB API endpoints.

## Project Structure

```
JMeter-The-Movie-DB/
├── README.md              # Documentation with API endpoint details and thread group settings
├── The Movie DB.jmx       # JMeter test plan (XML format, JMeter 5.6.2)
└── QWEN.md                # This context file
```

## Test Plan Configuration

### Thread Group Settings ("The movie db")

| Property | Value |
|----------|-------|
| Number of Threads (users) | 100 |
| Ramp-up period (seconds) | 200 |
| Loop Count | 10 |
| On Sample Error | Continue |

**Total simulated requests:** 100 threads × 10 loops × 4 HTTP samplers = **4,000 requests**

### API Endpoints Tested

| Sampler | Method | Endpoint | Expected Response |
|---------|--------|----------|-------------------|
| Now Playing | GET | `/3/movie/now_playing` | 200 |
| Top Rated | GET | `/3/movie/top_rated` | 200 |
| Upcoming | GET | `/3/movie/upcoming` | 200 |
| Add Rating | POST | `/3/movie/968051/rating` | 201 |

### Request Parameters

**GET Requests (Now Playing, Top Rated, Upcoming):**
- `language=en-US`
- `page=1`

**POST Request (Add Rating):**
```json
{
  "value": 8.0
}
```

### Authentication

All requests use Bearer token authentication via the `Authorization` header:
```
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9...
```

> ⚠️ **Security Note:** The JMX file contains a hardcoded API token. For production use, replace with environment variables or JMeter properties.

### Base URL Configuration

- **Host:** `api.themoviedb.org`
- **Port:** `443`
- **Protocol:** `https`

Configured via User Defined Variable: `baseUrlTMDB`

### Listeners Configured

| Listener | Purpose |
|----------|---------|
| Graph Results | Visualize response times over time |
| View Results Tree | Inspect individual request/response details |
| Assertion Results | View assertion pass/fail status |
| Aggregate Graph | Statistical summary graph |
| View Results in Table | Tabular view of all samples |

### Response Assertions

Each HTTP sampler has a Response Assertion checking the HTTP status code:
- GET endpoints: Assert status `200`
- POST endpoint: Assert status `201`

## Building and Running

### Prerequisites

- **Apache JMeter 5.6.2** or later
- Valid TMDB API token (update the token in the JMX file if the current one expires)

### Running the Test Plan

**GUI Mode (for development/debugging):**
```bash
jmeter -n -t "The Movie DB.jmx"
```

**Non-GUI Mode (for actual load testing):**
```bash
jmeter -n -t "The Movie DB.jmx" -l results.jtl -e -o ./report
```

| Flag | Description |
|------|-------------|
| `-n` | Non-GUI mode |
| `-t` | Specify test plan file |
| `-l` | Output results to JTL file |
| `-e` | Generate HTML report after test |
| `-o` | Output directory for HTML report |

### Modifying Test Parameters

To change load settings, edit the Thread Group in the JMX file or use JMeter GUI:
1. Open `The Movie DB.jmx` in JMeter
2. Modify Thread Group settings as needed
3. Save and run

## Development Conventions

### File Format
- JMX files are XML-based (JMeter 5.6.2 format)
- Can be edited via JMeter GUI or text editor

### Best Practices Observed
- Uses variables for base URL (`${baseUrlTMDB}`) for easy environment switching
- Response assertions on all samplers for automated validation
- Keep-alive enabled for realistic HTTP connection behavior
- Follow redirects enabled for proper HTTP handling

### Recommendations for Extension
1. **Token Management:** Externalize the API token to a properties file or environment variable
2. **CSV Data Sets:** Add CSV Data Set Config for dynamic movie IDs in the rating endpoint
3. **Timers:** Add think time (e.g., Constant Timer, Gaussian Random Timer) between requests
4. **Listeners:** Disable heavy listeners (View Results Tree) during actual load tests
5. **Error Handling:** Add Response Assertion for error rate thresholds

## Related Resources

- [JMeter Documentation](https://jmeter.apache.org/usermanual/index.html)
- [TMDB API Documentation](https://developer.themoviedb.org/docs)
- [YouTube Tutorial](https://youtu.be/3uVh1EvPoqw) - Video walkthrough of this test plan
