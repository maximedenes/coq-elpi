# Derive

This directory contains the elpi files implementing various automatic
derivation of terms.  The corresponding .v files, defining the Coq commands,
are in `theories/derive/`.


![Big picture](derive.svg)

## derive.isK

Given an inductive `I` type it generates for each constructor `K` a function to `bool` names `${prefix}K` where `prefix` is `is` by default. The type of `isK` is `forall params idxs, I params idxs -> bool`. Example:
```coq
Elpi derive.isK list. Print isnil. (*
isnil = 
  fun (A : Type) (i : list A) =>
    match i with
    | nil => true
    | (_ :: _)%list => false
    end
*)
```
coverage: ?? full CIC

## derive.projK

Given an inductive `I` type it generates for each constructor `K` and argument `i` of this constructor a function named `${prefix}Ki` where `prefix` is `proj` by default. The type of `projKi` is `forall params idxs default_value_for_args, I params idxs -> arg_i`. Example:
```coq
Elpi derive.projK Vector.t. Print projcons1. (*
projcons1 = 
  fun (A : Type) (H : nat) (h : A) (n : nat) (_ : Vector.t A n) (i : Vector.t A H) =>
    match i with
    | Vector.nil _ => h
    | Vector.cons _ h0 _ _ => h0
    end
 : forall (A : Type) (H : nat),
          A -> forall n : nat, Vector.t A n ->
          Vector.t A H -> A
```
The intended use is to perform injection, i.e. one aleady has a term of the shape `K args` and
can just use these args to provide the default values.

If the project argument's type depends on the value of other arguments, then it is boxed using `existT`.
```coq
Check projcons3. (*
projcons3
     : forall (A : Type) (H : nat),
       A -> forall n : nat, Vector.t A n ->
       Vector.t A H -> {i1 : nat & Vector.t A i1}
*)
```
coverage: ?? full CIC
