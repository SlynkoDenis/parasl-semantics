---
title: Language Type
---

```k
module TYPE
  imports DOMAINS
  imports FLOAT

  syntax TypeInt ::= "int"
  syntax Type ::= TypeInt

  syntax TypeFloat ::= "float"
  syntax Type ::= TypeFloat

  syntax TypeBool ::= "bool"
  syntax Type ::= TypeBool

  // syntax TypeArray ::= Type "[" Int "]"
  // syntax Type ::= TypeArray

  syntax Type ::= "#top" | "#bot"

  // numeric types conversion
  syntax Type ::= #leastCommonType(Type, Type)   [function, total]

  rule #leastCommonType(int, int) => int
  rule #leastCommonType(float, int) => float
  rule #leastCommonType(int, float) => float
  rule #leastCommonType(float, float) => float

  syntax Bool ::= #isNumericType(Type)            [function, total]

  rule #isNumericType(int) => true
  rule #isNumericType(float) => true
  rule #isNumericType(_) => false
    [owise]

  syntax Float ::= #toFloat(Int)                  [function, total]
  rule #toFloat(A) => Int2Float(A, 23, 8)
endmodule
```
