> Это категории **выражений**, а не типов.

каждое выражение обладает двумя независимыми свойствами: *type и value*

|                            lvalue                             |                               rvalue                                |
| :-----------------------------------------------------------: | :-----------------------------------------------------------------: |
|                        id (переменные)                        |                     литералы (кроме строковых)                      |
|                                                               |                 + - * / % >> << & \| ^ && \|\| ~ !                  |
|              = \*= += -= >>= <<= %= /= &= \|= ^=              |                           > < <= >= == !=                           |
|                      ++expr      --expr                       |                          expr++     expr--                          |
|                        *\ptr    a\[i]                         |                                 &a                                  |
|               запятая, если правая часть lvalue               |                  запятая, если правая часть rvalue                  |
|                 ?: если оба выражения lvalue                  |                ?: если хотя бы одно выражение rvalue                |
| function call when return type is T&<br> (or cast expression) | function call when return type is T or T&&<br> (or cast expression) |
|                                                               |                                                                     |