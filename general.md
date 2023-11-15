# General conventions

This document outlines the general conventions established within inVerita.

## Table of contents

[REST API URIs Naming](#rest-api-uris-naming)

---

## REST API URIs Naming

1. Separate words with hyphens  

    To make your URIs easy for people to scan and interpret, use the hyphen (-) character to improve the readability of names.

    :x: Bad  
    `http://localhost:65003/api/timeLog/usersByAdmin`

    :white_check_mark: Good  
    `http://localhost:65003/api/timeLog/users-by-admin`

2. Use nouns to represent resources  

    RESTful URI should refer to a resource that is a thing (noun) instead of referring to an action (verb) because nouns have properties that verbs do not have – similarly, resources have attributes.

    :x: Bad  
    `http://localhost:65003/api/getEmployees`

    :white_check_mark: Good  
    `http://localhost:65003/api/employees`

3. Use pluralized nouns for collection resources

    :x: Bad  
    `http://localhost:65003/api/employee/{id}`

    :white_check_mark: Good  
    `http://localhost:65003/api/employees/{id}`

4. Avoid using file extensions  

    File extensions look bad and do not add any advantage. Removing them decreases the length of URIs as well. No reason to keep them.

    :x: Bad  
    `http://localhost:65003/api/reports.json`

    :white_check_mark: Good  
    `http://localhost:65003/api/reports`

5. Do not use trailing forward slash  

    As the last character within a URI’s path, a forward slash (/) adds no semantic value and may confuse. It’s better to drop it from the URI.

    :x: Bad  
    `http://localhost:65003/api/emloyees/`

    :white_check_mark: Good  
    `http://localhost:65003/api/employees`

6. Use lowercase letters  

    When convenient, lowercase letters should be consistently preferred in URI paths.

    :x: Bad  
    `http://localhost:65003/api/RESOURCE`

    :white_check_mark: Good  
    `http://localhost:65003/api/resource`