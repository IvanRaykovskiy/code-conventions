# :zap: C# code conventions

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


## Overall rules
1. Prefer clarity over brevity
## Naming
1. Use PascalCase for class names and method names
2. Use camelCase for method arguments, local variables, and private fields.
3. Use PascalCase for constant names, both fields and local constants.
4. Private instance fields start with an underscore `_`
5. Avoid using single-letter names, almost in any situation it's better to use meaningful name even for small iteration  
   
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
1. Avoid using abbreviations or acronyms in names, except for widely known and accepted abbreviations
2. Use meaningful and descriptive namespaces that follow the reverse domain name notation
3. Choose assembly names that represent the primary purpose of the assembly


## Formatting
1. Use Allman style braces, where each brace begins on a new line. A single line statement block can go without braces but the block must be properly indented on its own line and must not be nested in other statement blocks that use braces. 
    - Never use single-line form (for example: if (source == null) throw new ArgumentNullException("source");)
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

1. Place method on new line (LINQ) and other .
2. TODO



#### Read more

