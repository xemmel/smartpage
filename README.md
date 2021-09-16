## Smartpage
## C# Sessions


## Table of Content
1. [Tasks](#tasks)



## Tasks

### Wrap a syncronic method with a Task

```csharp

public int Add(int x, int y)
{
    Thread.Sleep(3000);
    return (x+y);
}

public Task<int> AddAsync(int x, int y)
{
    return Task<int>.Run(() => Add(x, y));
}

```


[Back to top](#table-of-content)