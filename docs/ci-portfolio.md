# Continuous Intelligence Portfolio

Branton Dawson

2026-04

This page summarizes my work on **continuous intelligence** projects.

## 1. Professional Project

### Repository Link


[Getting_started_Project:](https://github.com/bjdawson23/cintel-01-getting-started)

### Brief Overview of Project Tools and Choices

**working files:**

- data/ - it starts with the data
- docs/ - tell the story
- artifacts/ - store the results
- src/cintel/pipeline_dawson.py - where the magic happens
- pyproject.toml
- zensical.toml

#### Environment and dependency management: uv

This repo standardizes on uv for Python version pinning, dependency sync, running scripts, and tooling. The main benefit is reproducibility and one consistent workflow across Windows, macOS, and Linux.

#### Python runtime and packaging choices

The project targets Python 3.14+ and uses a src-layout package structure with setuptools via pyproject-based configuration. That gives clean imports, predictable builds, and compatibility with modern Python tooling.

#### Documentation tooling: Zensical

Docs are built with Zensical for Python API documentation generation from source/docstrings. This keeps narrative docs and code docs aligned and easy to publish.

#### Logger setup

- Step-by-step progress logs
- Each pipeline stage writes INFO messages:

1. data loaded
2. signal engineering started
3. anomaly checks and thresholds used
4. anomaly count
5. summary complete
6. artifact/chart write confirmation
7. pipeline success and END main()

## 2. Anomaly Detection

### Repository Link

[Anomaly Detection:](https://github.com/bjdawson23/cintel-02-static-anomalies)

### Techniques

**Signals**

- I added logic to check if the height is TOO LOW or TOO HIGH.

**Experiments**

- Testing for height under 15 inches was classified as too low. A reason column was added explaining which value(s) triggered the anomaly.

### Artifacts

[Anomaly_Detection:](https://github.com/bjdawson23/cintel-02-static-anomalies/tree/main/artifacts)

- The code found 8 rows with anomalies. Two of the rows had multiple anomalies for age and height.

### Insights

- The 8 flagged records reveal likely data entry errors in the clinic's patient records system. Ages of 94, 120, and 220 years are impossible for a pediatric clinic serving children up to age 16, suggesting likely transcription or keying mistakes were made.

- A height of 5 inches and a height of 14 inches are unlikely for any full-term child, pointing to unit errors or keystroke mistakes. Heights of 84 and 95 inches (7–8 feet) are similarly impossible for pediatric patients.

- For the clinic, this means the data pipeline successfully identified records that should be reviewed and corrected before being used in any analysis, reporting, or billing.

- For Business Intelligence purposes, implementing this anomaly check as a routine step would help protect against data quality issues.

## 3. Signal Design

### Repository Link

[Signal Desing:](https://github.com/bjdawson23/cintel-03-signal-design)

### Signals

The Disney signal pipeline creates the following fields:

    wait_minutes: cleaned wait time (non-negative integer minutes)
    is_long_wait: True when wait is 45 minutes or more
    land_avg_wait: mean wait for all rides in the same land at the same timestamp
    wait_bucket: categorical wait class (low < 15, medium 15-29, high 30-59, extreme >= 60)

I chose these signals to help:

    1. identify rides with severe current waits
    2. compare each ride with its land-level average wait
    3. summarize status quickly with bucketed labels
    4. identify peak congestion windows by attraction using the heatmap

### Artifacts

[Artifacts:](https://github.com/bjdawson23/cintel-03-signal-design/tree/main/artifacts)

- The evidence produced custumer wait times by day and ride and that helped label buckets for wait time.

### Insights

Together, the signals can help support better staffing, communication, and crowd-flow decisions across the park.

## 4. Rolling Monitoring

### Repository Link

[Rolling Monitoring:](https://github.com/bjdawson23/cintel-04-rolling-monitoring)

### Techniques

**Air Quality Analysis - Kansas City 2023**
This project includes an analysis of Kansas City air quality data from the first half of 2023, focusing on rolling metrics for carbon monoxide (CO) and the Air Quality Index (AQI).

Signals (Rolling Metrics)

Two 30-day rolling metrics were computed to smooth daily variation and reveal trends:

    30-Day Rolling CO Mean (co_rolling_30d_mean)
        Average of the most recent 30 daily max 8-hour CO concentrations
        Units: ppm (parts per million)
        Reveals baseline CO exposure trends; rising values indicate worsening air quality from CO

    30-Day Rolling AQI Mean (aqi_rolling_30d_mean)
        Average of the most recent 30 daily AQI values
        Dimensionless index (typically 0-500+)
        Provides overall health-facing air quality trend; integrates effects of all pollutants

### Artifacts

[Artifacts:](https://github.com/bjdawson23/cintel-04-rolling-monitoring/tree/main/artifacts)

![AirQuality](https://github.com/bjdawson23/cintel-04-rolling-monitoring/blob/main/artifacts/air_quality_rolling_chart.png)

### Insights

- Strong correlation: CO and AQI trends track closely together, indicating CO is a significant driver of KC's overall AQI
- Seasonal pattern: Clear degradation Feb-Mar 2023 (winter), improvement Apr-Aug 2023 (spring/summer), slight worsening toward year-end

## 5. Drift Detection

### Repository Link

[Drift Detection:](https://github.com/bjdawson23/cintel-05-drift-detection)

### Techniques

Two signals were created

    ORDERS_DRIFT_THRESHOLD: Final[float] = 250.0
    PRODUCTION_DRIFT_THRESHOLD: Final[float] = 300.0

    Summarize each period with means.
    Compute difference of means.
    Flag drift if threshold exceeded.

### Artifacts

[Artifacts:](https://github.com/bjdawson23/cintel-05-drift-detection/tree/main/artifacts)

This drift detection found that orders were not drifting. It did find that production numbers were drifting downward. This signals a decrease in production.

### Insights

As an analyst, this could help with production forecasting and detecting drift patterns.

## 6. Continuous Intelligence Pipeline

### Repository Link

[Continuous Intelligence Pipeline:](https://github.com/bjdawson23/cintel-06-continuous-intelligence)

### Techniques

- Dataset:  System Metrics Data

    Data represents recent observations from a monitored system.

    Each row represents one observation of system activity.

    The CSV file includes these columns:
        requests: number of requests handled
        errors: number of failed requests
        total_latency_ms: total response time in milliseconds

- Signals

I added a new anomaly for sudden request jumps between consecutive rows and wiring it into both logging and the final summary.

- Experiments

Originally set the threshold to: MAX_REQUEST_JUMP_PCT: Final[float] = 0.08. This created 40 anomolies. I tried 0.10 next and still had 40 anomolies. At 0.03, it ended up with 10 anomolies. I settled at 0.12 and 7 anomolies.

### Artifacts

[Artifacts:](https://github.com/bjdawson23/cintel-06-continuous-intelligence/tree/main/artifacts)

### Assessment

The threshold tuning can make the new request-jump anomaly rule less sensitive by having it set in the .08-.12 range for this test. I also added a line chart to view error rate and latency per network request. The chart shows peak errors occuring at observation 30.
