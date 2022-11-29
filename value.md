---
title: Values
---

```k
requires "type.md"

module BIT
  import DOMAINS
  imports FLOAT

  syntax Int ::= #bitsInInt(Int) [function, total]

  // rule #bitsInInt(N) => 8
  //   requires N >=Int -128 andBool N <=Int 127

  // TODO: add other cases, look at domains.md and try to optimize
  // it using absInt and log2Int standard functions

  rule #bitsInInt(N) => log2Int(N) +Int 1
    requires N >=Int 0

  rule #bitsInInt(N) => log2Int(absInt(N) -Int 1) +Int 1
    requires N <Int 0


  // floating point numbers

  syntax Int ::= #bitsInExponent(Float) [function, total]

  // float
  rule #bitsInExponent(F) => 8
    requires exponentBitsFloat(F) <=Int 8 andBool precisionFloat(F) <=Int 23

  // double
  rule #bitsInExponent(F) => 11
    requires exponentBitsFloat(F) <=Int 11 andBool precisionFloat(F) <=Int 52 [owise]

  syntax Int ::= #bitsInMantissa(Float) [function, total]

  // float
  rule #bitsInMantissa(F) => 23
    requires exponentBitsFloat(F) <=Int 8 andBool precisionFloat(F) <=Int 23

  // double
  rule #bitsInMantissa(F) => 52
    requires exponentBitsFloat(F) <=Int 11 andBool precisionFloat(F) <=Int 52 [owise]
endmodule

module VALUE-SYNTAX
  import TYPE-SYNTAX
  import INNER-TYPE-SYNTAX
  import DOMAINS
  imports FLOAT

  syntax OuterValueInt ::= Int
  syntax OuterValue ::= OuterValueInt

  syntax OuterInitializersList ::= List{OuterValue, ","}
                                 | "{" OuterInitializersList "}" [bracket]

  syntax InnerValueInt ::= Int
  syntax InnerValue ::= InnerValueInt

  syntax InnerTypedValue ::= value(InnerType, InnerValue)

  syntax KResult ::= InnerTypedValue

  syntax InnerTypedValue ::= #toInnerValue(OuterValue) [function]
  // TODO: add other cases

  syntax OuterValueFloat ::= Float
  syntax OuterValue ::= OuterValueFloat

  syntax InnerValueFloat ::= Float
  syntax InnerValue ::= InnerValueFloat

  syntax Bool ::= #validInnerTypedValue(InnerTypedValue) [function, total]

endmodule

module VALUE
  import BIT
  imports VALUE-SYNTAX
  imports TYPE

  rule #toInnerValue(V:OuterValue) => value(int(#bitsInInt({V}:>Int)), {V}:>Int)
    requires isOuterValueInt(V)

  rule #toInnerValue(V:OuterValue) => value(float(#bitsInExponent({V}:>Float), #bitsInMantissa({V}:>Float)), {V}:>Float)
    requires isOuterValueFloat(V)

  rule #validInnerTypedValue(value(_Type:InnerTypeFloat, _Value:InnerValueFloat)) => true
  rule #validInnerTypedValue(value(_Type:InnerTypeInt, _Value:InnerValueInt)) => true [owise]
  rule #validInnerTypedValue(_) => false [owise]

  configuration <k> #toInnerValue($PGM:OuterValue) </k>

endmodule
```