# Analysis Report Template

When analysis succeeds, produce `analysis_report.md` with this structure:

```markdown
# Request Analysis Report

## Basic Information

URL:
Method:
Source:
Status:

## Parsed Request

Headers:
Cookies:
Query:
Body:
Response:

## Parameter Analysis

| Parameter | Location | Type | Change Pattern | Importance | Handling |
|---|---|---|---|---|---|

## Failure Diagnosis

| Step | Finding | Evidence | Probability |
|---|---|---|---|

## Browser Requirement

Can run without browser: YES/NO/UNKNOWN

Missing dependencies:

## Recommended Plan

## Project Structure

## Usage

## Example Code

## Tests

## Stability Notes

## Risks

## Alternatives

## Maintenance Points
```
