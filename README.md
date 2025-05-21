# Grand Spider

A Flask-based API that uses OpenAI's GPT models to analyze and describe web pages based on their HTML content.

## Overview

Grand Spider is a web service that fetches HTML content from a specified URL and uses OpenAI's language models to generate a concise description of what's on the page. This is useful for content categorization, web indexing, or quickly understanding a webpage's purpose without having to visit it.

## Features

- **URL Analysis**: Submit any public URL and receive an AI-generated description of its content
- **OpenAI Integration**: Leverages GPT-4.1-nano for intelligent content analysis
- **API Authentication**: Secure endpoints with API key authentication
- **Error Handling**: Comprehensive error handling for various failure scenarios
- **Logging**: Detailed logging for monitoring and debugging

## Technology Stack

- **Python 3.x**
- **Flask**: Web framework for the API
- **OpenAI API**: For AI-powered content analysis
- **Requests**: For fetching web content
- **python-dotenv**: For secure environment variable management

## Installation & Setup

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/yourusername/grand_spider.git
   cd grand_spider
   ```

2. **Create a Virtual Environment**:
   ```bash
   python -m venv venv
   # On Windows:
   venv\Scripts\activate
   # On macOS/Linux:
   source venv/bin/activate
   ```

3. **Install Dependencies**:
   ```bash
   pip install flask openai requests python-dotenv
   ```

4. **Create Environment Variables**:
   Create a `.env` file in the project root with:
   ```
   SERVICE_API_KEY=your_chosen_api_key_here
   OPENAI_API_KEY=your_openai_api_key_here
   
   # 'True' for debugging - Optional - Default: False
   DEBUG=False

   # Optional - e.g. '/usr/bin/chromedriver'
   # Set the path if "driver not found" error is encountered.
   DRIVER_PATH=path_to_chromedriver
   ```

## API Usage

### Analyze a Company Website

**Endpoint**: `/api/analyze-company`

**Method**: `POST`

**Headers**:
- `Content-Type: application/json`
- `api-key: your_chosen_api_key_here`

**Request Body**:
```json
{
  "url": "https://example.com",
  "max_pages": 10,             // Optional, default is 10
  "use_selenium": false        // Optional, default is false. Set true for JS-heavy sites.
}
```

**Example Request (curl)**:
```bash
curl -X POST "http://localhost:5000/api/analyze-company" \
  -H "Content-Type: application/json" \
  -H "api-key: your_chosen_api_key_here" \
  -d '{"url": "https://example.com", "max_pages": 10, "use_selenium": false}'
```

**Success Response**:
```json
{
  "message": "Company analysis job started successfully.",
  "job_id": "550e8400-e29b-41d4-a716-446655440000",
  "status_url": "/api/analyze-company/550e8400-e29b-41d4-a716-446655440000"
}
```

### Get Analysis Status

**Endpoint**: `/api/analyze-company/<job_id>`

**Method**: `GET`

**Headers**:
- `api-key: your_chosen_api_key_here`

**Success Response**:
```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "url": "https://example.com",
  "max_pages": 10,
  "use_selenium": false,
  "status": "completed",
  "created_at": 1234567890.123,
  "found_pages_count": 5,
  "analyzed_pages": [...],
  "final_summary": "Company description...",
  "error": null,
  "crawler_used": "simple"
}
```

### List All Jobs

**Endpoint**: `/api/jobs`

**Method**: `GET`

**Headers**:
- `api-key: your_chosen_api_key_here`

**Success Response**:
```json
{
  "total_jobs": 2,
  "jobs": [
    {
      "job_id": "550e8400-e29b-41d4-a716-446655440000",
      "url": "https://example.com",
      "status": "completed",
      "crawler_used": "simple",
      "created_at": 1234567890.123,
      "finished_at": 1234567895.123,
      "duration_seconds": 5.0,
      "error": null
    }
  ]
}
```

### Health Check

**Endpoint**: `/api/health`

**Method**: `GET`

**Success Response**:
```json
{
  "status": "ok",
  "message": "Company Analyzer API is running",
  "service_api_key_status": "configured",
  "openai_client_status": "initialized",
  "selenium_support": "available"
}
```

## Error Handling

The API returns appropriate HTTP status codes and descriptive error messages:

- **400**: Bad Request (invalid URL, missing parameters)
- **401**: Unauthorized (invalid or missing API key)
- **404**: Not Found (job ID not found)
- **405**: Method Not Allowed
- **500**: Internal Server Error (unexpected issues)
- **502**: Bad Gateway (issues fetching URL content)
- **503**: Service Unavailable (OpenAI API issues)
- **504**: Gateway Timeout (URL fetch timeout)

## Deployment

For production deployment:

1. **Set Environment Variables**:
   Ensure `SERVICE_API_KEY` and `OPENAI_API_KEY` are set in your production environment.

2. **Run with a Production WSGI Server**:
   ```bash
   pip install gunicorn  # on Linux/macOS
   gunicorn -w 4 -b 0.0.0.0:5000 grand_spider:app
   ```
   
   For Windows, consider using waitress:
   ```bash
   pip install waitress
   waitress-serve --host=0.0.0.0 --port=5000 grand_spider:app
   ```

## Configuration Options

The following environment variables can be configured:

- `SERVICE_API_KEY`: Required for authentication
- `OPENAI_API_KEY`: Required for OpenAI API access
- `OPENAI_MODEL`: The OpenAI model to use (default: "gpt-4.1-nano-2025-04-14")
- `REQUEST_TIMEOUT`: Timeout for URL fetching in seconds (default: 15)
- `MAX_CONTENT_LENGTH`: Maximum HTML content length to analyze (default: 15000)
- `MAX_RESPONSE_TOKENS`: Maximum tokens in the OpenAI response (default: 300)

## License

[MIT License](https://opensource.org/licenses/MIT)

## Disclaimer

This tool is designed for analyzing publicly accessible web content. Always respect robots.txt, website terms of service, and avoid excessive requests to the same domain.

