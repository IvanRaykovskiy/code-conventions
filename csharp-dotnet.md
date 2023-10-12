# :zap:C# code conventions

A code standard is essential for development team code readability, consistency, and collaboration. Following industry standards and established guidelines makes code easier to understand, maintain, and extend. Most projects utilize code conventions to enforce a uniform style. Here you can find recommendations defined within inVerita

> [!NOTE]  
> Tools can help your team enforce your standards. You can enable code analysis to enforce the rules 
> you prefer. You can also create an editorconfig so that Visual Studio automatically enforces your 
> style guidelines. As a starting point, you can copy the dotnet/docs repo's file to use Microsoft style.
>  
> These tools make it easier for your team to adopt your preferred guidelines. Visual Studio applies 
> the rules in all .editorconfig files in scope to format your code. You can use multiple 
> configurations to enforce corporate-wide standards, team standards, and even granular project 
> standards

## Table of contents

[Overall rules](#overall-rules)  
[Naming](#naming)  
[Types](#types)  
[Formatting](#formatting)  
[Strings](#strings)  
[LINQ](#linq)  

---


## Overall rules
1. Prefer clarity over brevity
2. Avoid obsolete or outdated language constructs
3. Only catch exceptions that can be properly handled; avoid catching generic exceptions.
4. Use specific exception types to provide meaningful error messages
5. Use asynchronous programming with async and await for CPU and I/O-bound operations
6. Use the language keywords for data types instead of the runtime types. For example, use `string` instead of `System.String`, or `int` instead of `System.Int32`
7. Avoid overly complex and convoluted code logic
## Naming
1. Use PascalCase for class names and method names
2. Use camelCase for method arguments, local variables, and private fields.
3. Private instance fields start with an underscore `_`
4. Use PascalCase for constant names, both fields and local constants.
5. When naming an interface, use pascal casing in addition to prefixing the name with an I. This clearly indicates to consumers that it's an interface: `ITeamService`, `INotifyManager`, etc.
6. Avoid using single-letter names, almost in any situation it's better to use meaningful name even for small iteration  
   
    :x: Bad  
    ```csharp
    for (var i = 1; i <= days; i++)
    {
        worksheet.Cells[row, column].LoadFromText(i.ToString());
        column++;
    }
    ```
    :white_check_mark: Good  

    ```csharp
    for (var day = 1; day <= daysInMomth; day++)
    {
        worksheet.Cells[row, column].LoadFromText(day.ToString());
        column++;
    }
    ```
7. Avoid using abbreviations or acronyms in names, except for widely known and accepted abbreviations
8. Use meaningful and descriptive namespaces that follow the reverse domain name notation
9. Choose assembly names that represent the primary purpose of the assembly

## Types
1. Use var only when a reader can infer the type from the expression. Try to give either self-explanatory name or set type explicitly instead of `var` (or both)     

    :x: Bad  
    ```csharp
    // 1st example (redundant)
    List<ProjectViewModel> projectViewModels = new List<ProjectViewModel>();

    // 2nd example (too generic)
    var entity = GetEntityById(projectId);
    ```
    :white_check_mark: Good  

    ```csharp
    var projectViewModels = new List<ProjectViewModel>();

    // 2nd example (good name and method name)
    var project = GetProjectById(projectId);
    ```

2. Use `new()` initialization syntax in explicit way and try to avoiding its usage without type definition

    :x: Bad  
    ```csharp
    _maps = new()
    {
        { PaidVacation, VacationType.Normal },
        { SickLeave, VacationType.SickLeave },
        { NonPaidVacation, VacationType.WithoutPay },
    };
    ```
    :white_check_mark: Good  

    ```csharp
    _maps = new()
    {
        { PaidVacation, VacationType.Normal },
        { SickLeave, VacationType.SickLeave },
        { NonPaidVacation, VacationType.WithoutPay },
    };
    ```

3. use explicit typing to determine the type of the loop variable in foreach loops
4. use int
5. int vs inst32.convert (floor vs round)
6. ...
7. use private by default + setters

8.  do not throw;

## Formatting
1. Use Allman style braces, where each brace begins on a new line. A single line statement block can go without braces but the block must be properly indented on its own line and must not be nested in other statement blocks that use braces. 
    - Never use single-line form  

        :x: Bad  
        ```csharp
        if (source == null) throw new ArgumentNullException("source");
        ```

        :white_check_mark: Good  
        ```csharp
        if (source == null) 
            throw new ArgumentNullException("source");
        ```
    - Using braces is always accepted, and required if any block of an if/else if/.../else compound statement uses braces or if a single statement body spans multiple lines.
    - Braces may be omitted only if the body of every block associated with an if/else if/.../else compound statement is placed on a single line.

        :x: Bad  
        ```csharp
        if (timelog.Type == TimelogTypeData.TimeLog)
            timelogModel.TimeLogHours = timelog.Hours;
        else if (timelog.Type == TimelogTypeData.Overtime)
        {
            timelogModel.OvertimeHours = timelog.Hours;
        }
        ```

        :white_check_mark: Good  
        ```csharp
        if (timelog.Type == TimelogTypeData.TimeLog)
        {
            timelogModel.TimeLogHours = timelog.Hours;
        }
        else if (timelog.Type == TimelogTypeData.Overtime)
        {
            timelogModel.OvertimeHours = timelog.Hours;
        }
        ```
> [!NOTE]  
> One exception is that a using statement is permitted to be nested within another using
> statement by starting on the following line at the same indentation level, even if the
> nested using contains a controlled block

2. Write only one statement per line.
3. Write only one declaration per line.
4. Add at least one blank line between method definitions and property definitions
5. Use braces to explicitly show required execution order without guessing operators priority
   
    :x: Bad  
    ```csharp
    private bool DatesIntersected(DateTime rangeStart, DateTime rangeEnd, DateTime from, DateTime to)
    {
        return rangeStart >= from && rangeStart <= to || rangeEnd >= from && rangeEnd <= to;
    }
    ```

    :white_check_mark: Good  
    ```csharp
    private bool DatesIntersected(DateTime rangeStart, DateTime rangeEnd, DateTime from, DateTime to)
    {
        return (rangeStart >= from && rangeStart <= to) || (rangeEnd >= from && rangeEnd <= to);
    }
    ```

6. TODO


## Coding
1. Prefer constructors over initialization to prevent bugs and to abstract user from how this object should be initialized  

    :x: Bad  
    ```csharp
    userLeaveDays.Select(l => new VacationViewModel
            {
                StartDate = l.StartsAt,
                EndDate = l.EndsAt,
                UserId = user.Id,
                Status = TicketStatusType.Approved,
            }).ToList();
    ```

    :white_check_mark: Good  
    ```csharp
    userLeaveDays.Select(l => new Vacation(user.Id, leave, TicketStatusType.Approved))
                 .ToList();

    // VacationViewModel.cs
    public class VacationViewModel
    {
        public VacationViewModel(Guid userId, LeaveDay leave, TicketStatusType statusType)
        {
            UserId = userId;
            StartDate = leave.StartsAt;
            EndDate = leave.EndsAt;
            Status = statusType;
        }
    }
    ```
## Strings

1. Use string interpolation to concatenate short strings, as shown in the following code  

    :x: Bad  
    ```csharp
    var message = "Project:" + projectName "is Closed";
    ```

    :white_check_mark: Good  
    ```csharp
    var message = $"Project: {projectName} is Closed";
    ```

2. Use `string.Empty` instead of just `""`
3. Use `StringBuilder` to append strings in loops, especially when you're working with large amounts of text  
   
   :x: Bad  
    ```csharp
    string output = string.Empty;
    foreach(Project project in ProjectList)
    {
        output += $"<h2>Project name: {project.Name}</h2>";
    }
    ```

    :white_check_mark: Good  
    ```csharp
    var sb = new StringBuilder();
    foreach(Project project in ProjectList)
    {
        sb.Append($"<h2>Project name: {project.Name}</h2>");
    }
    ```

    > [!IMPORTANT]  
    > There is some overhead associated with creating a StringBuilder object, both in time and memory.  
    > As a rule of thumb use StringBuilder if you need to dynamically concatenate more than 7-10 strings. 
    > In other cases just use `+` as it is optimized to `Concat()` method
    
4. Use `nameof` instead of string names of values or properties  

    :x: Bad  
    ```csharp
    if(status == TicketStatus.Approved)
    {
        return $"Your ticket was Approved";
    }

    ```

    :white_check_mark: Good  
    ```csharp
    if(status == TicketStatus.Approved)
    {
        return $"Your ticket was {nameof(TicketStatus.Approved)}";
    }
    ```
   

## LINQ

1. Use multiline for better readability of LINQ extension methods

    :x: Bad  
    ```csharp
    var adminClaims = _dbContext.Claims.Where(c => c.UserId == adminUserId).Select(c => c.ClaimValue).ToList();
    ```

    :white_check_mark: Good  
    ```csharp
    var adminClaims = _dbContext.AspNetUsersClaims
                            .Where(c => c.UserId == adminUserId)
                            .Select(c => c.ClaimValue)
                            .ToList();
    ```

## Comments
1. Prefer method extraction with appropriate name instead of putting inline comments with explanation  

    :x: Bad  
    ```csharp
    // Checking dates intersection before doing work
    if((leave.Start >= fromDate && leave.Start <= toDate) || (leave.End >= fromDate && leave.End <= toDate))
    {
        // Do some work
    }
    ```

    :white_check_mark: Good  
    ```csharp
    if(DatesIntersected(leave.Start, leave.End, fromDate, toDate))
    {
        // Do some work
    }

    private bool DatesIntersected(DateTime rangeStart, DateTime rangeEnd, DateTime from, DateTime to)
    {
        return (rangeStart >= from && rangeStart <= to) || (rangeEnd >= from && rangeEnd <= to);
    }

    ```
2. Place the comment on a separate line, not at the end of a line of code.
3. Make 1 blank line between comment and previous line of code

#### Read more

