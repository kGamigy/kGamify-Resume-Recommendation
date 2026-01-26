# Resume Recommendation API

A FastAPI-based REST API that intelligently recommends the best-matched resumes for a given job posting using semantic similarity and multi-criteria matching.

## Overview

This application leverages machine learning models to analyze job requirements and applicant resumes, providing intelligent resume recommendations. It uses semantic embeddings, multi-language support, and a weighted scoring system to match candidates with job positions.

## Features

- **Semantic Job-Resume Matching**: Uses sentence transformers to understand contextual similarity between job descriptions and resumes
- **Multi-Language Support**: Automatically detects and translates non-English resumes and job descriptions to English
- **Multi-Criteria Scoring**: Combines three scoring dimensions:
  - **Skill Score** (50% weight): Matches required skills with resume content
  - **Experience Score** (25% weight): Compares required experience with applicant background
  - **Education Score** (25% weight): Evaluates educational requirements match
- **PDF Resume Processing**: Automatically extracts text from PDF resumes
- **MongoDB Integration**: Stores and retrieves job and applicant data
- **RESTful API**: Easy-to-use endpoints with automatic documentation

## Project Structure

```
Latest_resume_api/
├── main.py                 # FastAPI application entry point
├── recommendation.py       # Core recommendation logic
├── utils.py               # Utility functions (PDF extraction, translation, matching)
├── requirements.txt       # Python dependencies
└── Documents/            # Additional documentation files
```

## Installation

### Prerequisites
- Python 3.8 or higher
- MongoDB (running and accessible)
- Virtual environment (recommended)

### Setup Steps

1. **Clone/Navigate to the project**:
   ```bash
   cd d:\kGamify\Resume_Recommendation\Latest_resume_api
   ```

2. **Activate virtual environment** (Windows):
   ```bash
   venv\Scripts\activate
   ```
   
   For macOS/Linux:
   ```bash
   source venv/bin/activate
   ```

3. **Install dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

4. **Configure environment variables**:
   Create a `.env` file in the project root:
   ```
   MONGO_URI=
   DB_NAME=
   ```

## Running the Application

1. **Start the API server**:
   ```bash
   uvicorn main:app --reload
   ```
   
   The server will start at `http://127.0.0.1:8000`

2. **Access the API**:
   - **OpenAPI Interactive Docs**: [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)
   - **ReDoc Documentation**: [http://127.0.0.1:8000/redoc](http://127.0.0.1:8000/redoc)

## API Endpoints

### Get Resume Recommendations

**Endpoint**: `GET /recommend`

**Query Parameters**:
- `job_id` (required, string): The unique identifier of the job posting
- `top_n` (optional, integer, default=5): Number of top recommendations to return

**Request Example**:
```
GET /recommend?job_id=JOB123&top_n=5
```

**Response Example**:
```json
{
  "job_id": "JOB123",
  "recommendations": [
    {
      "applicant_id": "APP001",
      "name": "John Doe",
      "experience": "5 years in software development",
      "education": "Bachelor's in Computer Science",
      "resume_url": "https://example.com/resume.pdf",
      "skill_score": 0.87,
      "exp_score": 0.92,
      "edu_score": 0.88,
      "final_score": 0.8825
    }
  ]
}
```

## Dependencies

- **fastapi** (0.95.2): Modern web framework for building APIs
- **pymongo** (4.3.3): Python driver for MongoDB
- **uvicorn** (0.22.0): ASGI server for running FastAPI
- **sentence-transformers** (2.2.2): Pre-trained semantic similarity models
- **torch** (2.1.0): Deep learning framework
- **PyPDF2** (3.0.1): PDF text extraction
- **requests** (2.31.0): HTTP library for downloading resumes
- **deep-translator** (1.10.1): Language translation
- **langdetect** (1.0.9): Language detection
- **python-dotenv** (1.0.0): Environment variable management
- **huggingface_hub** (0.16.4): Hugging Face models access

## How It Works

### Recommendation Algorithm

1. **Retrieves job details** from MongoDB using the provided job_id
2. **Fetches all applicants** who applied for that specific job
3. **Processes each applicant**:
   - Extracts text from PDF resume
   - Translates all text to English (if needed)
   - Encodes text using sentence transformers
   - Calculates skill match using cosine similarity
   - Evaluates experience match against job requirements
   - Evaluates education match against job requirements
4. **Combines scores** using weighted formula:
   ```
   final_score = (0.5 × skill_score) + (0.25 × exp_score) + (0.25 × edu_score)
   ```
5. **Returns top N candidates** sorted by final score

### Semantic Matching

Uses the `all-MiniLM-L6-v2` model to generate embeddings and calculates cosine similarity between:
- Job skills and resume content
- Job description and resume content
- Required experience and applicant experience
- Required education and applicant education

## Database Schema

### Jobs Collection
```json
{
  "_id": "job_id",
  "title": "Software Engineer",
  "description": "We are looking for...",
  "skills": ["Python", "FastAPI", "MongoDB"],
  "eligibility": "3+ years experience in software development",
  "education": "Bachelor's in Computer Science or related field"
}
```

### Applicants Collection
```json
{
  "_id": "applicant_id",
  "name": "John Doe",
  "experience": "5 years as a full-stack developer",
  "education": "Bachelor's in CS from MIT",
  "resume_url": "https://example.com/resume.pdf",
  "applied_jobs": ["job_id_1", "job_id_2"]
}
```

## Limitations

- Requires active MongoDB connection
- PDF extraction quality depends on PDF format
- Translation accuracy depends on the source language
- Recommendation quality depends on resume content completeness
- API does not store recommendation history by default

For detailed limitations, see `Documents/Limitations.docx`

## Error Handling

The API handles errors gracefully and returns meaningful error messages:

```json
{
  "error": "Job not found" 
}
```

## Configuration

All configuration is managed through environment variables in the `.env` file:
- `MONGO_URI`: MongoDB connection string
- `DB_NAME`: Database name in MongoDB

## Performance Considerations

- First request may take longer due to model initialization
- Sentence transformer model is loaded once and reused
- PDF extraction and translation add processing time per applicant
- Consider caching results for frequently requested jobs

## Future Enhancements

- Add result caching mechanism
- Implement batch processing for multiple jobs
- Add more detailed scoring breakdowns
- Support additional file formats (DOCX, TXT)
- Add applicant filtering by location, salary expectations, etc.
- Implement weighted custom scoring

## Support & Documentation

For additional information, refer to:
- `Documents/Resume_Recommendation.docx` - Detailed project documentation
- `Documents/Run the Project Locally (Windows).docx` - Windows-specific setup guide

## License

[Add appropriate license information]

## Contributors

[Add contributor information]


