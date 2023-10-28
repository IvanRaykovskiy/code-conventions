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
[Coding](#coding)  
[Strings](#strings)  
[Async code](#async-code)  
[Exceptions](#exceptions)  
[LINQ](#linq)  

---


## Overall rules
1. Prefer clarity over brevity
2. Avoid overly complex and convoluted code logic
3. Avoid obsolete or outdated language constructs
4. Only catch exceptions that can be properly handled; avoid catching generic exceptions.
5. Use specific exception types to provide meaningful error messages
6. Use asynchronous programming with `async` and `await` for CPU and I/O-bound operations
7. Use the language keywords for data types instead of the runtime types. For example, use `string` instead of `System.String`, or `int` instead of `System.Int32`
## Naming
1. Use PascalCase for class names and method names
2. Use camelCase for method arguments, local variables, and private fields.
3. Use `_` (underscore)  for private instance fields
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

2. From C# 9.0 you also can use `new()` initialization syntax in explicit way. But try to avoiding its usage without type definition

    :x: Bad  
    ```csharp
    // ids was declared earlier in file
    ids = new();    
    ```
    :white_check_mark: Good  

    ```csharp
    List<int> ids = new();
    ```

3. From C# 6.0 prefer using key value based syntax for Dictionary initialization

    :x: Bad  
    ```csharp
    var koef = new Dictionary<string, int>();
    koef["first"] = 10;
    koef["second"] = 20;
    koef["third"] = 30; 
    ```
    :white_check_mark: Good  

    ```csharp
    var koef = new Dictionary<string, int>
    {
        ["first"] = 10,
        ["second"] = 20,
        ["third"] = 30
    };
    ```

4. Use explicit typing to determine the type of the loop variable in foreach loops if it's not clear from context (e.g. ids could be int, string or guid)

    :x: Bad  
    ```csharp
    foreach(var id in userIds)
    {
    }
    ```

    :white_check_mark: Good  
    ```csharp
    foreach(Guid id in userIds)
    {
    }
    ```

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

6. Use file scoped namespace declarations (available from C# 10) to reduce code nesting  
   
    :x: Bad  
    ```csharp
    namespace InVerita.Implementations.VacationService
    {
        public class HolidayService : IHolidayService
        {
            public List<Holiday> GetHolidays() => _appDbContext.Holidays().ToList();
            //...
        }
    }
    ```

    :white_check_mark: Good  
    ```csharp
    namespace InVerita.Implementations.VacationService
    
    public class HolidayService : IHolidayService
    {
        public List<Holiday> GetHolidays() => _appDbContext.Holidays().ToList();
        //...
    }    
    ```

## Coding
1. Use proper and explicit access modifiers depend on need and try to use the most restricted access available for this declaration
     - Prefer `internal` over `public` if possible
     - Prefer `protected` over `public` if possible
     - Prefer `private setters` as default instead of making all public
2. User `private` keyword explicitly
2. Prefer constructors over initialization to prevent bugs and to abstract user from how this object should be initialized  

    :x: Bad  
    ```csharp
    userLeaveDays.Select(leave => new VacationViewModel
    {
        StartDate = leave.StartsAt,
        EndDate = leave.EndsAt,
        UserId = user.Id,
        Status = TicketStatusType.Approved,
    }).ToList();
    ```

    :white_check_mark: Good  
    ```csharp
    userLeaveDays.Select(leave => new Vacation(user.Id, leave, TicketStatusType.Approved))
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
3. Use `Func<>` and `Action<>` in-built delegates instead of defining your own delegate types whenever possible

    :x: Bad  
    ```csharp
    public delegate double CalculateDelegate(int value1, int value2);

    public static double Calculate(int value1, int value2)
    {
        return value1 * value2;
    }

    CalculateDelegate ProcessMath = Calculate;
    double result = ProcessMath(16, 4);
    ```

    :white_check_mark: Good  
    ```csharp
    public static double Calculate(int value1, int value2)
    {
        return value1 * value2;
    }

    Func<int, int, double> ProcessMath = new(Calculate);
    double result = ProcessMath(16, 4);
    ```
    > [!NOTE]  
    > You can violate above rule in case you really need named custom delegate, that will be used in various places helping you define business/domain logic
4. Do not return `true`/`false` if you already have a boolean expression

    :x: Bad  
    ```csharp
    if (day.Date > currentDate.Date)
        return true;
    else
        return false;
    ```

    :white_check_mark: Good  
    ```csharp
    return day.Date > currentDate.Date;
    ```
5. Use `&&` instead of `&` and `||` instead of `|` when you perform comparisons

    :x: Bad  
    ```csharp
    if (teamLead != null & teamLead.DeleteAt == string.Empty)
    {
        reporter = teamLead.Id;
    }
    ```

    :white_check_mark: Good  
    ```csharp
    if (teamLead != null && teamLead.DeleteAt == string.Empty)
    {
        reporter = teamLead.Id;
    }
    ```

    > [!NOTE]  
    > If the `teamLead` is null, the second clause in the if statement would cause a run-time error. But the `&&` operator
    > short-circuits when the first expression is false. That is, it doesn't evaluate the second expression. 
    > The `&` operator would evaluate both, resulting in a run-time error when `teamLead` is null.

6. Use DependencyInjection instead of creating instances for loose coupling and better code maintanability and testability
   
   :x: Bad  
    ```csharp
    public class ReportExporter : IExporter
    {
        private readonly IStageService _stageService;

        public ReportExporter(IStageService stageService)
        {
            _stageService = new StageService();
        }

        public async Task<MemoryStream> GetReport(DateTime month, CancellationToken token)
        {    
            var filtered = await _stageService.GetReport(month, token);
            // ...
        }
    }
    ```

    :white_check_mark: Good  
    ```csharp
    public class ReportExporter : IExporter
    {
        private readonly IStageService _stageService;

        public ReportExporter(IStageService stageService)
        {
            _stageService = stageService;
        }

        public async Task<MemoryStream> GetReport(DateTime month, CancellationToken token)
        {    
            var filtered = await _stageService.GetReport(month, token);
            // ...
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
    > There is some overhead associated with creating a `StringBuilder` object, both in time and memory.  
    > As a rule of thumb use `StringBuilder` if you need to dynamically concatenate more than 7-10 strings. 
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
## Async code
1. Await tasks **always** for preventing unpredictable results
2. Return only `Task` or `Task<T>` from async method
3. Use postfix `Async` in method namings only if you have mix of sync and async methods or any kind of migration. If your code is written with initial async execution - avoid this postfix in method names
4. Provide `CancellationToken` to methods that could be interrupted
5. Use `WhenAny`, `WhenAll` methods instead of `WaitAny` and `WaitAll` to avoid blocking
6. Use `ConfigureAwait(false)` only for purpose, e.g. in .NET Core and above versions there is no synchronization context - so it's no needed in most cases. For details check documentation
7. Create a task to execute code using `Task.Run` not `Task constructor` or `Task.Start`

## Exceptions
1. Handle common conditions without throwing exceptions - exceptions are more expensive

    :x: Bad  
    ```csharp
    try
    {
        dbConnection.Close();
    }
    catch (InvalidOperationException ex)
    {
        Console.WriteLine(ex.Message);
    }
    ```
    :white_check_mark: Good  
    ```csharp
    if (dbConnection.State != ConnectionState.Closed)
    {
        dbConnection.Close();
    }
    ```
2. Keep the original stack trace information with the exception, by using the `throw` statement without specifying the exception or re-throwing it

    :x: Bad  
    ```csharp
    try
    {
        
    }
    catch (ArgumentException ex)
    {
        throw ex;
    }
    ```

    :white_check_mark: Good  
    ```csharp
    try
    {
        
    }
    catch (ArgumentException)
    {
        throw;
    }
    ```
3. In a lot of cases try to return `null` (or `default`) instead of throwing an exception. Also you ca return Empty collections or use **Null object pattern**
4. Use the **predefined** .NET exception types. Introduce a new exception class only when a predefined one doesn't apply. For example:
   - If a property set or method call isn't appropriate given the object's current state, throw an InvalidOperationException exception.
   - If invalid parameters are passed, throw an ArgumentException exception or one of the predefined classes that derive from ArgumentException.
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
2. Use meaningful names for query variables

    :x: Bad  
    ```csharp
    var seattleCustomers = from c in customers
                           where c.City == "Seattle"
                           select c.Name;
    ```

    :white_check_mark: Good  
    ```csharp
    var seattleCustomers = from customer in customers
                           where customer.City == "Seattle"
                           select customer.Name;
    ```
3. Use IQueryable and IEnumerable accordingly. Prefer filtering occurring on Database or any other provider

     :x: Bad  
    ```csharp
    public List<User> GetUsers() => 
        _dbContext.Users.ToList()
                        .Where(user => user.IsActive)
                        .ToList();
    ```

    :white_check_mark: Good  
    ```csharp
     public List<User> GetUsers() => 
        _dbContext.Users.Where(user => user.IsActive)
                        .ToList();
    ```
4. Use multiple from clauses instead of a join clause to access inner collections. For example, a collection of Student objects might each contain a collection of test scores
   
   :x: Bad  
    ```csharp
    var scoreQuery = from student in students
                     join score in student.Scores on student.Id equals score.studentId
                     where score > 90
                     select new { LastName = student.LastName, score };
    ```

    :white_check_mark: Good  
    ```csharp
    var scoreQuery = from student in students
                     from score in student.Scores
                     where score > 90
                     select new { LastName = student.LastName, score };
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

