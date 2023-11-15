# General conventions

This document outlines the general conventions established within inVerita.

## Table of contents

[REST API Naming](#rest-api-naming)

---

## REST API Naming

1. Separate words with hyphens

    :x: Bad  
    `http://localhost:65003/api/timeLog/usersByAdmin`

    :white_check_mark: Good  
    `http://localhost:65003/api/timeLog/users-by-admin`

2. Use nouns to represent resources

    :x: Bad  
    `http://localhost:65003/api/getEmployees`

    :white_check_mark: Good  
    `http://localhost:65003/api/employees`

3. Use pluralized nouns for resources when possible unless they are singleton resources

    :x: Bad  
    `http://localhost:65003/api/employee/{id}`

    :white_check_mark: Good  
    `http://localhost:65003/api/employees/{id}`

4. Avoid using file extensions

    :x: Bad  
    `http://localhost:65003/api/reports.json`

    :white_check_mark: Good  
    `http://localhost:65003/api/reports`