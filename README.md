# Smart Resume Screener

An AI-powered full-stack application that helps recruiters analyze resumes against job descriptions and quickly understand how well a candidate fits a particular role.

## Project Overview

Smart Resume Screener automates the initial stage of candidate evaluation. Instead of manually reading every resume, recruiters can upload a candidate's PDF resume and provide a job description. The application extracts relevant information from the resume and uses Google Gemini to evaluate the candidate against the requirements of the position.

The system generates:

* Candidate contact information
* Technical and professional skills
* Educational background
* Previous work experience
* Overall compatibility score
* Skills that match the job requirements
* Skills that are missing
* AI-generated reasoning for the score

All analyzed candidates are stored in MongoDB so recruiters can review previous screening results through the application dashboard.

---

## Key Capabilities

### Resume Analysis

The application accepts resumes in PDF format and extracts their textual content using `pdf-parse`.

After extraction, the resume information is processed by the AI layer to identify important candidate details.

### AI-Based Candidate Matching

Google Gemini performs semantic analysis between the resume and the supplied job description.

The AI evaluates:

* Candidate skills
* Relevant professional experience
* Educational qualifications
* Required technologies
* Job-specific requirements

The result is returned as structured JSON so that the frontend can easily display the analysis.

### Candidate Scoring

Each candidate receives a match score between **1 and 10**.

The system also displays:

* Matched skills
* Missing skills
* A concise explanation of the score

This allows recruiters to understand not only the score but also the reasons behind it.

### Candidate History

Every completed analysis is saved in MongoDB.

The dashboard allows users to review:

* Candidate names
* Match scores
* Extracted skills
* Analysis information
* Screening timestamps

---

## System Architecture

```text
                 ┌───────────────────────┐
                 │      React + Vite     │
                 │     Frontend UI       │
                 └───────────┬───────────┘
                             │
                       Axios / HTTP
                             │
                             ▼
                 ┌───────────────────────┐
                 │    Node.js + Express  │
                 │      REST API         │
                 └───────────┬───────────┘
                             │
              ┌──────────────┼───────────────┐
              │              │               │
              ▼              ▼               ▼
        ┌───────────┐  ┌─────────────┐  ┌───────────┐
        │ pdf-parse │  │ Gemini API  │  │ MongoDB   │
        │ PDF Text  │  │ AI Analysis │  │ Database  │
        └───────────┘  └─────────────┘  └───────────┘
                             │
                             ▼
                    Structured Analysis
                             │
                             ▼
                    Candidate Dashboard
```

---

## Technology Used

### Frontend

* React
* Vite
* Axios
* HTML
* CSS

### Backend

* Node.js
* Express.js
* Multer
* pdf-parse

### Database

* MongoDB
* Mongoose
* MongoDB Atlas

### Artificial Intelligence

* Google Gemini API
* Structured JSON responses

---

## How the Application Works

The complete screening process follows these steps:

```text
1. Recruiter opens the application
              ↓
2. PDF resume is uploaded
              ↓
3. Job description is entered
              ↓
4. Frontend sends the data to the backend
              ↓
5. Multer processes the uploaded PDF
              ↓
6. pdf-parse extracts resume text
              ↓
7. Resume text and job description are sent to Gemini
              ↓
8. Gemini performs candidate analysis
              ↓
9. Backend validates and normalizes the response
              ↓
10. Analysis is stored in MongoDB
              ↓
11. Results are sent back to the frontend
              ↓
12. Candidate analysis is displayed
```

---

## AI Processing

Gemini is responsible for transforming unstructured resume information into structured candidate data.

The AI extracts:

* Full name
* Email
* Phone number
* Skills
* Education
* Work experience

It then compares the extracted information with the job description and produces an evaluation.

### AI Output

The expected response follows this structure:

```json
{
  "candidate": {
    "name": "",
    "email": "",
    "phone": ""
  },
  "skills": [],
  "education": [
    {
      "degree": "",
      "institution": "",
      "duration": "",
      "details": ""
    }
  ],
  "experience": [
    {
      "role": "",
      "company": "",
      "duration": "",
      "summary": ""
    }
  ],
  "matchScore": 0,
  "matchedSkills": [],
  "missingSkills": [],
  "justification": ""
}
```

The backend normalizes the returned information before storing it in MongoDB.

---

## Prompt Design

The Gemini prompt instructs the model to:

1. Analyze only information available in the resume.
2. Compare the resume with the provided job description.
3. Avoid creating or assuming candidate information.
4. Return a score from 1 to 10.
5. Identify relevant matching skills.
6. Identify important missing skills.
7. Generate a short explanation.
8. Return the result as JSON.

This structured approach makes the AI response easier for the backend to validate and the frontend to consume.

---

## Database Model

Each screening operation creates an analysis document.

```text
Analysis
│
├── fileName
├── jobDescription
├── resumeText
│
├── candidate
│   ├── name
│   ├── email
│   └── phone
│
├── skills[]
│
├── education[]
│   ├── degree
│   ├── institution
│   ├── duration
│   └── details
│
├── experience[]
│   ├── role
│   ├── company
│   ├── duration
│   └── summary
│
├── matchScore
├── matchedSkills[]
├── missingSkills[]
├── justification
└── createdAt
```

---

## REST API

### Health Check

```http
GET /api/health
```

Example:

```json
{
  "ok": true,
  "service": "smart-resume-screener"
}
```

This endpoint can be used to verify that the backend server is available.

### Resume Analysis

```http
POST /api/analyze
```

Form-data:

| Field            | Type | Description          |
| ---------------- | ---- | -------------------- |
| `resume`         | File | Candidate PDF resume |
| `jobDescription` | Text | Job requirements     |

The endpoint:

* Validates the uploaded file
* Checks the job description
* Extracts PDF text
* Sends information to Gemini
* Processes the AI response
* Saves the result
* Returns the analysis

### Candidate History

```http
GET /api/candidates
```

Returns previously analyzed candidates stored in MongoDB.

---

## Project Layout

```text
smart-resume-screener/
│
├── backend/
│   ├── server.js
│   ├── package.json
│   ├── package-lock.json
│   └── .env.example
│
├── frontend/
│   ├── src/
│   │   ├── App.jsx
│   │   ├── main.jsx
│   │   └── styles.css
│   ├── index.html
│   ├── package.json
│   └── package-lock.json
│
├── .gitignore
└── README.md
```

---

## Installation

### Requirements

Before starting the application, install:

* Node.js
* npm
* MongoDB Atlas account
* Google AI Studio / Gemini API credentials

### Clone the Project

```bash
git clone https://github.com/Shareef-16/smart-resume-screener.git
cd smart-resume-screener
```

### Configure Backend

```bash
cd backend
npm install
```

Create a `.env` file:

```env
PORT=5000
MONGO_URI=your_mongodb_connection_string
GEMINI_API_KEY=your_gemini_api_key
GEMINI_MODEL=gemini-3.5-flash-lite
CLIENT_URL=http://localhost:5173
```

Start the backend:

```bash
npm run dev
```

The API will be available at:

```text
http://localhost:5000
```

### Configure Frontend

Open another terminal:

```bash
cd frontend
npm install
npm run dev
```

The frontend will normally be available at:

```text
http://localhost:5173
```

---

## Environment Configuration

| Variable         | Description                     |
| ---------------- | ------------------------------- |
| `PORT`           | Port used by the Express server |
| `MONGO_URI`      | MongoDB Atlas connection string |
| `GEMINI_API_KEY` | Google Gemini API credential    |
| `GEMINI_MODEL`   | Gemini model used for analysis  |
| `CLIENT_URL`     | Frontend URL permitted by CORS  |

> Do not commit the actual `.env` file to GitHub.

Use `.env.example` to document the required configuration without exposing credentials.

---

## Validation and Error Handling

The backend checks several failure conditions before completing an analysis.

It handles:

* Missing resume
* Unsupported file types
* Excessively large PDFs
* Missing job descriptions
* PDFs containing no extractable text
* Missing Gemini credentials
* Empty AI responses
* Invalid JSON returned by Gemini
* MongoDB connection problems

These checks help prevent invalid screening results and provide a more reliable application flow.

---

## Example

A candidate applying for a full-stack development position might receive:

```text
Match Score: 7/10

Matched Skills:
React
Node.js
MongoDB
SQL
Git

Missing Skills:
Spring Boot
Docker

Explanation:
The candidate demonstrates strong full-stack development
experience and satisfies several major requirements of the
position. However, experience with Spring Boot and Docker
is not demonstrated in the resume.
```

---

## Security Considerations

Credentials are kept outside the source code through environment variables.

The repository ignores:

```text
.env
.env.*
node_modules/
dist/
```

The Gemini API key and MongoDB credentials should never be committed to a public repository.

---

## Future Enhancements

Potential improvements include:

* Bulk resume processing
* Candidate ranking
* Advanced filtering
* Recruiter login and authentication
* Exporting screening reports
* Candidate comparison
* Category-based scoring
* Job recommendation
* Cloud deployment
* Improved recruiter dashboard

---

## Demo Flow

A short project demonstration can be completed as follows:

```text
Open Application
      ↓
Upload Resume
      ↓
Enter Job Description
      ↓
Run Analysis
      ↓
Display Candidate Information
      ↓
Show Match Score
      ↓
Show Matched / Missing Skills
      ↓
Show AI Explanation
      ↓
Open Previous Candidates
      ↓
Verify Stored MongoDB Data
```

---

## License

This project is developed as an academic and portfolio demonstration of AI-assisted recruitment technology.
