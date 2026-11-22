# Jenkins: Jobs, Pipelines and the Jenkinsfile

---

## Table of Contents
1. [Your First Job: Freestyle Project](#your-first-job-freestyle-project)
2. [Freestyle Job with Git](#freestyle-job-with-git)
3. [What is a Pipeline?](#what-is-a-pipeline)
4. [Your First Pipeline — Step by Step](#your-first-pipeline--step-by-step)
5. [Declarative Pipeline Syntax Explained](#declarative-pipeline-syntax-explained)
6. [agent — Where to Run](#agent--where-to-run)
7. [stages, stage and steps](#stages-stage-and-steps)
8. [Common Steps](#common-steps)
9. [environment — Variables](#environment--variables)
10. [Built-in Jenkins Variables](#built-in-jenkins-variables)
11. [parameters — Ask for Input Before a Build](#parameters--ask-for-input-before-a-build)
12. [when — Run a Stage Only Sometimes](#when--run-a-stage-only-sometimes)
13. [input — Wait for Approval](#input--wait-for-approval)
14. [post — Run Steps After the Build](#post--run-steps-after-the-build)
15. [options — Timeouts, Log Rotation, Timestamps](#options--timeouts-log-rotation-timestamps)
16. [triggers — Start Builds Automatically](#triggers--start-builds-automatically)
17. [Credentials — Use Secrets Safely](#credentials--use-secrets-safely)
18. [Pipeline Script from SCM (Jenkinsfile in Git)](#pipeline-script-from-scm-jenkinsfile-in-git)
19. [Multibranch Pipeline](#multibranch-pipeline)
20. [Pipeline Syntax Helper (Snippet Generator)](#pipeline-syntax-helper-snippet-generator)
21. [Reading Build Results](#reading-build-results)
22. [Incident: Pipeline Fails on Docker Permission](#incident-pipeline-fails-on-docker-permission)
23. [Quick Reference](#quick-reference)
