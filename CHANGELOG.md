Version TODO
============

The library has been tested using Agda version TODO.

Important changes since 0.16:

Non-backwards compatible changes
--------------------------------

#### Overhaul of safety of the library

* Currently the library is very difficult to type check with the `--safe`
  flag as there are unsafe functions scattered throughout the key modules.
  This means that it is almost impossible to verify the safety of any code
  depending on the standard library. The following reorganisation will fix
  this problem after the **next** full release of Agda. (Agda 2.5.4.1 uses
  `postulate`s in the `Agda.Builtin.X` that will be removed in the next release).

* The following new `Unsafe` modules have been created. Nearly all of these
  are all marked as unsafe as they use the `trustMe` functionality, either for
  performance reasons or for informative decidable equality tests.
  ```
  Data.Char.Unsafe
  Data.Float.Unsafe
  Data.Nat.Unsafe
  Data.Nat.DivMod.Unsafe
  Data.String.Unsafe
  Data.Word.Unsafe
  ```

* The other modules affected are `Relation.Binary.HeterogeneousEquality.Quotients(.Examples)`
  which previously postulated function extensionality. The relevant submodules
  now take extensionality as a module parameter instead of postulating it. If you
  want to use these results then you should postulate it yourself.

* The full list of unsafe modules is:
  ```
  Data.Char.Unsafe
  Data.Float.Unsafe
  Data.Nat.Unsafe
  Data.Nat.DivMod.Unsafe
  Data.String.Unsafe
  Data.Word.Unsafe
  IO
  IO.Primitive
  Reflection
  Relation.Binary.PropositionalEquality.TrustMe
  ```

#### New codata library

* A new `Codata` library has been added that is based on copatterns and sized
  types rather than musical notation . The library is built around a generic
  notion of coinductive `Thunk` and provides the basic data types:
  ```agda
  Codata.Thunk

  Codata.Colist
  Codata.Conat
  Codata.Cofin
  Codata.Covec
  Codata.Delay
  Codata.M
  Codata.Stream
  ```
  Each coinductive type comes with a notion of bisimilarity in the corresponding
  `Codata.X.Bisimilarity` module and at least a couple of proofs demonstrating
  how they can be used in `Codata.X.Properties`. This library is somewhat
  experimental and may undergo minor changes in later versions.

* To avoid confusion, the old codata modules that previously lived in the `Data`
  directory have been moved to the folder `Codata.Musical`
  ```agda
  Coinduction ↦ Codata.Musical.Notation
  Data.Cofin  ↦ Codata.Musical.Cofin
  Data.Colist ↦ Codata.Musical.Colist
  Data.Conat  ↦ Codata.Musical.Conat
  Data.Covec  ↦ Codata.Musical.Covec
  Data.M      ↦ Codata.Musical.M
  Data.Stream ↦ Codata.Musical.Stream
  ```

* Each new-style coinduction type comes with two functions (`fromMusical` and
  `toMusical`) converting back and forth between old-style coinduction values
  and new-style ones.

* The type `Costring` and method `toCostring` have been moved from `Data.String`
  to a new module `Codata.Musical.Costring`.

* The `Rec` construction has been dropped from `Codata.Musical.Notation` as the
  `--guardedness-preserving-type-constructors` flag which made it useful has been
  removed from Agda.

#### Improved consistency between `Data.(List/Vec).(Any/All/Membership)`

* Added new module `Data.Vec.Any`.

* The type `_∈_` has been moved from `Data.Vec` to the new module
  `Data.Vec.Membership.Propositional` and has been reimplemented using
  `Any` from `Data.Vec.Any`. In particular this means that you must now
  pass a `refl` proof to the `here` constructor.

* The proofs associated with `_∈_` have been moved from `Data.Vec.Properties`
  to the new module  `Data.Vec.Membership.Propositional.Properties`
  and have been renamed as follows:
  ```agda
  ∈-++ₗ      ↦ ∈-++⁺ˡ
  ∈-++ᵣ      ↦ ∈-++⁺ʳ
  ∈-map      ↦ ∈-map⁺
  ∈-tabulate ↦ ∈-tabulate⁺
  ∈-allFin   ↦ ∈-allFin⁺
  ∈-allPairs ↦ ∈-allPairs⁺
  ∈⇒List-∈   ↦ ∈-toList⁺
  List-∈⇒∈   ↦ ∈-fromList⁺
  ```

* The proofs `All-universal` and `All-irrelevance` have been moved from
  `Data.(List/Vec).All.Properties` and renamed `universal` and `irrelevant` in
  `Data.(List/Vec).All`.

* The existing function `tabulate` in `Data.Vec.All` has been renamed
  `universal`. The name `tabulate` now refers to a function with following type:
  ```agda
  tabulate : (∀ i → P (lookup i xs)) → All P xs
  ```

#### Deprecating `Data.Fin.Dec`:

* This module has been deprecated as its non-standard position
  was causing dependency cycles. The move also makes finding
  subset properties easier.

* The following proofs have been moved to `Data.Fin.Properties`:
  ```
  decFinSubset, any?, all?, ¬∀⟶∃¬-smallest, ¬∀⟶∃¬
  ```

* The following proofs have been moved to `Data.Fin.Subset.Properties`:
  ```
  _∈?_, _⊆?_, nonempty?, anySubset?, decLift
  ```
  The latter has been renamed to `Lift?`.

* The file `Data.Fin.Dec` still exists for backwards compatibility
  and exports all the old names, but may be removed in some
  future version.

#### Rearrangement of algebraic Solvers

* Standardised and moved the generic solver modules as follows:
  ```agda
  Algebra.RingSolver                        ↦ Algebra.Solver.Ring
  Algebra.Monoid-solver                     ↦ Algebra.Solver.Monoid
  Algebra.CommutativeMonoidSolver           ↦ Algebra.Solver.CommutativeMonoid
  Algebra.IdempotentCommutativeMonoidSolver ↦ Algebra.Solver.IdempotentCommutativeMonoid
  ```

* In order to avoid dependency cycles, special instances of solvers for the following
  data types have been moved from `Data.X.Properties` to new modules `Data.X.Solver`.
  The naming conventions for these solver modules have also been standardised.
  ```agda
  Data.Bool.Properties.RingSolver          ↦  Data.Bool.Solver.∨-∧-Solver
  Data.Bool.Properties.XorRingSolver       ↦  Data.Bool.Solver.xor-∧-Solver
  Data.Integer.Properties.RingSolver       ↦  Data.Integer.Solver.+-*-Solver
  Data.List.Properties.List-solver         ↦  Data.List.Solver.++-Solver
  Data.Nat.Properties.SemiringSolver       ↦  Data.Nat.Solver.+-*-Solver
  Function.Related.TypeIsomorphisms.Solver ↦ Function.Related.TypeIsomorphisms.Solver.×-⊎-Solver
  ```

* Renamed `Algebra.Solver.Ring.Natural-coefficients` to `Algebra.Solver.Ring.NaturalCoefficients`.

#### Overhaul of `Data.X.Categorical`

* Added new modules:
  ```
  Category.Comonad

  Data.List.NonEmpty.Categorical
  Data.Maybe.Categorical
  Data.Product.Categorical.Left
  Data.Product.Categorical.Right
  Data.Product.N-ary.Categorical
  Data.Sum.Categorical.Left
  Data.Sum.Categorical.Right
  Data.These.Categorical.Left
  Data.These.Categorical.Right

  Codata.Colist.Categorical
  Codata.Covec.Categorical
  Codata.Delay.Categorical
  Codata.Stream.Categorical
  ```

* In `Data.List.Categorical` renamed:
  ```agda
  sequence ↦ sequenceM
  ```

* Moved `monad` from `Data.List.NonEmpty` to `Data.List.NonEmpty.Categorical`.

* Moved `functor`, `monadT`, `monad`, `monadZero` and `monadPlus` from `Data.Maybe`
  to `Data.Maybe.Categorical`.

* Created new module `Function.Identity.Categorical` and merged the existing modules
  `Category.Functor.Identity` and `Category.Monad.Identity` into it.


#### Overhaul of `Data.Container`, `Data.W` and `Codata.(Musical.)M`

* Made `Data.Container` (and associated modules) more level-polymorphic

* Created `Data.Container.Core` for the core definition of `Container`,
  container morphisms `_⇒_`, `All` and `Any`. This breaks the dependency cycle
  with `Data.W` and `Codata.Musical.M`.

* Refactored `Data.W` and `Codata.Musical.M` to use `Container`.

#### Overhaul of `Relation.Binary.Indexed` subtree

* The module `Relation.Binary.Indexed` has been renamed
  `Relation.Binary.Indexed.Heterogeneous`.

* The names `REL`, `Rel`, `IsEquivalence` and `Setoid` in
  `Relation.Binary.Indexed.Heterogeneous` and `Relation.Binary.Indexed.Homogeneous`
  have been deprecated in favour of `IREL`, `IRel`, `IsIndexedEquivalence` and
  `IndexedSetoid`. This should significantly improves code readability and avoid
  confusion with the contents of `Relation.Binary`. The old names still exist
  but have been deprecated.

* The record `IsIndexedEquivalence` in `Relation.Binary.Indexed.Homogeneous`
  is now implemented as a record encapsulating indexed versions of the required
  properties, unlike the old version which directly indexed equivalences.

* In order to avoid dependency cycles, the `Setoid` record in `Relation.Binary`
  no longer exports `indexedSetoid`.  Instead the corresponding indexed setoid can
  be constructed using the `setoid` function in
  `Relation.Binary.Indexed.Heterogeneous.Construction.Trivial`.

* The function `_at_` in `Relation.Binary.Indexed.Heterogeneous` has been moved to
  `Relation.Binary.Indexed.Heterogeneous.Construction.At` and renamed to `_atₛ_`.

#### Other

* The `Data.List.Relation.Sublist` directory has been moved to
  `Data.List.Relation.Sublist.Extensional` to make room for the
  new `Data.List.Relation.Sublist.Inductive` hierarchy.

* The types `IrrelevantPred` and `IrrelevantRel` in
  `Relation.Binary.PropositionalEquality` have both been renamed to
  `Irrelevant` and have been moved to `Relation.Unary` and
  `Relation.Binary` respectively.

* Removed `Data.Char.Core` which was doing nothing of interest.

* In `Data.Maybe.Base` the `Set` argument to `From-just` has been made implicit
  to be consistent with the definition of `Data.Sum`'s `From-injₙ`.

* In `Data.Product` the function `,_` has been renamed to `-,_` to avoid
  conflict with the right section of `_,_`.

* Made `Data.Star.Decoration`, `Data.Star.Environment` and `Data.Star.Pointer`
  more level polymorphic. In particular `EdgePred` now takes an extra explicit
  level parameter.

* In `Level` the target level of `Lift` is now explicit.

* In `Function` the precedence level of `_$_` (and variants) has been changed to `-1`
  in order to improve its interaction with `_∋_` (e.g. `f $ Maybe A ∋ do (...)`).

* `Relation.Binary` now no longer exports `_≡_`, `_≢_` and `refl`. The standard
  way of accessing them remains `Relation.Binary.PropositionalEquality`.

* The syntax `∀[_]` in `Relation.Unary` has been renamed to `Π[_]`. The original
  name is now used for for implicit universal quantifiers.

Other major changes
-------------------

* Added new module `Algebra.Properties.CommutativeMonoid`. This contains proofs
  of lots of properties of summation, including 'big summation'.

* Added new modules `Data.List.Relation.Permutation.Inductive(.Properties)`,
  which give an inductive definition of permutations over lists.

* Added a new module `Data.These` for the classic either-or-both Haskell datatype.

* Added new module `Data.List.Relation.Sublist.Inductive` which gives
  an inductive definition of the sublist relation (i.e. order-preserving embeddings).
  We also provide a solver for this order in `Data.List.Relation.Sublist.Inductive.Solver`.

* Added new module `Relation.Binary.Construction.Converse`. This is very similar
  to the existing module `Relation.Binary.Flip` in that it flips the relation. However
  unlike the existing module, the new module leaves the underlying equality unchanged.

* Added new modules `Relation.Unary.Closure.(Preorder/StrictPartialOrder)` providing
  closures of a predicate with respect to either a preorder or a strict partial order.

* Added new modules `Relation.Binary.Properties.(DistributiveLattice/HeytingAlgebra)`.

Deprecated features
-------------------

The following deprecations have occurred as part of a drive to improve consistency across
the library. The deprecated names still exist and therefore all existing code should still
work, however they have been deprecated and use of any new names is encouraged. Although not
anticipated any time soon, they may eventually be removed in some future release of the library.

* All deprecated names now give warnings at point-of-use when type-checked.

* In `Data.Nat.Divisibility`:
  ```
  nonZeroDivisor-lemma
  ```

* In `Data.Nat.Properties`:
  ```agda
  i∸k∸j+j∸k≡i+j∸k
  im≡jm+n⇒[i∸j]m≡n
  ```

* In `Function.Related`:
  ```agda
  preorder              ↦ R-preorder
  setoid                ↦ SR-setoid
  EquationReasoning.sym ↦ SR-sym
  ```

* In `Function.Related.TypeIsomorphisms`:
  ```agda
  ×-CommutativeMonoid    ↦ ×-commutativeMonoid
  ⊎-CommutativeMonoid    ↦ ⊎-commutativeMonoid
  ×⊎-CommutativeSemiring ↦ ×-⊎-commutativeSemiring
  ```

* In `Relation.Binary.Lattice`:
  ```agda
  BoundedJoinSemilattice.joinSemiLattice ↦ BoundedJoinSemilattice.joinSemilattice
  BoundedMeetSemilattice.meetSemiLattice ↦ BoundedMeetSemilattice.meetSemilattice
  ```

Other minor additions
---------------------

* Added new records to `Algebra`:
  ```agda
  record RawSemigroup c ℓ : Set (suc (c ⊔ ℓ))
  record RawGroup     c ℓ : Set (suc (c ⊔ ℓ))
  record RawSemiring  c ℓ : Set (suc (c ⊔ ℓ))
  ```

* Added new function `Category.Functor`'s `RawFunctor`:
  ```agda
  _<&>_ : F A → (A → B) → F B
  ```

* Added new function to `Category.Monad.Indexed`:
  ```agda
  RawIMonadT : (T : IFun I f → IFun I f) → Set (i ⊔ suc f)
  ```

* Added new function to `Category.Monad`:
  ```agda
  RawMonadT : (T : (Set f → Set f) → (Set f → Set f)) → Set _
  ```

* Added new functions to `Codata.Delay`:
  ```agda
  alignWith : (These A B → C) → Delay A i → Delay B i → Delay C i
  zip       : Delay A i → Delay B i → Delay (A × B) i
  align     : Delay A i → Delay B i → Delay (These A B) i
  ```

* Added new functions to `Codata.Musical.M`:
  ```agda
  map    : (C₁ ⇒ C₂) → M C₁ → M C₂
  unfold : (S → ⟦ C ⟧ S) → S → M C
  ```

* Added new proof to `Data.Fin.Permutation`:
  ```agda
  refute : m ≢ n → ¬ Permutation m n
  ```
  Additionally the definitions `punchIn-permute` and `punchIn-permute′`
  have been generalised to work with heterogeneous permutations.

* Added new proof to `Data.Fin.Properties`:
  ```agda
  toℕ-fromℕ≤″ : toℕ (fromℕ≤″ m m<n) ≡ m

  pigeonhole  : m < n → (f : Fin n → Fin m) → ∃₂ λ i j → i ≢ j × f i ≡ f j
  ```

* Added new proofs to `Data.List.Any.Properties`:
  ```agda
  here-injective  : here  p ≡ here  q → p ≡ q
  there-injective : there p ≡ there q → p ≡ q

  singleton⁺      : P x → Any P [ x ]
  singleton⁻      : Any P [ x ] → P x
  ++-insert       : P x → Any P (xs ++ [ x ] ++ ys)
  ```

* Added new functions to `Data.List.Base`:
  ```agda
  uncons : List A → Maybe (A × List A)
  head   : List A → Maybe A
  tail   : List A → Maybe (List A)
  ```

* Added new functions to `Data.List.Categorical`:
  ```agda
  functor     : RawFunctor List
  applicative : RawApplicative List
  monadT      : RawMonadT (_∘′ List)
  sequenceA   : RawApplicative F → List (F A) → F (List A)
  mapA        : RawApplicative F → (A → F B) → List A → F (List B)
  forA        : RawApplicative F → List A → (A → F B) → F (List B)
  forM        : RawMonad M → List A → (A → M B) → M (List B)
  ```

* Added new proofs to `Data.List.Membership.(Setoid/Propositional).Properties`:
  ```agda
  ∈-insert : v ≈ v′ → v ∈ xs ++ [ v′ ] ++ ys
  ∈-∃++    : v ∈ xs → ∃₂ λ ys zs → ∃ λ w → v ≈ w × xs ≋ ys ++ [ w ] ++ zs
  ```

* Added new functions to `Data.List.NonEmpty`:
  ```agda
  uncons    : List⁺ A → A × List A
  concatMap : (A → List⁺ B) → List⁺ A → List⁺ B
  ```

* Added new function to `Data.Maybe.Base`:
  ```agda
  fromMaybe : A → Maybe A → A
  ```

* Added new operator to `Data.Nat.Base`:
  ```agda
  ∣_-_∣ : ℕ → ℕ → ℕ
  ```

* Added new proofs to `Data.Nat.Divisibility`:
  ```agda

  n∣m⇒m%n≡0 : suc n ∣ m → m % (suc n) ≡ 0
  m%n≡0⇒n∣m : m % (suc n) ≡ 0 → suc n ∣ m
  m%n≡0⇔n∣m : m % (suc n) ≡ 0 ⇔ suc n ∣ m
  ```

* Added new operations and proofs to `Data.Nat.DivMod`:
  ```agda
  _%_ : (dividend divisor : ℕ) {≢0 : False (divisor ≟ 0)} → ℕ

  a≡a%n+[a/n]*n : a ≡ a % suc n + (a div (suc n)) * suc n
  a%1≡0         : a % 1 ≡ 0
  a%n<n         : a % suc n < suc n
  n%n≡0         : suc n % suc n ≡ 0
  a%n%n≡a%n     : a % suc n % suc n ≡ a % suc n
  [a+n]%n≡a%n   : (a + suc n) % suc n ≡ a % suc n
  [a+kn]%n≡a%n  : (a + k * (suc n)) % suc n ≡ a % suc n
  kn%n≡0        : k * (suc n) % suc n ≡ 0
  %-distribˡ-+  : (a + b) % suc n ≡ (a % suc n + b % suc n) % suc n
  ```

* Added new proofs to `Data.Nat.Properties`:
  ```agda
  *-distribˡ-∸ : _*_ DistributesOverˡ _∸_
  *-distrib-∸  : _*_ DistributesOver _∸_
  ^-*-assoc    : (m ^ n) ^ p ≡ m ^ (n * p)

  n≡m⇒∣n-m∣≡0       : n ≡ m → ∣ n - m ∣ ≡ 0
  m≤n⇒∣n-m∣≡n∸m     : m ≤ n → ∣ n - m ∣ ≡ n ∸ m
  ∣n-m∣≡0⇒n≡m       : ∣ n - m ∣ ≡ 0 → n ≡ m
  ∣n-m∣≡n∸m⇒m≤n     : ∣ n - m ∣ ≡ n ∸ m → m ≤ n
  ∣n-n∣≡0           : ∣ n - n ∣ ≡ 0
  ∣n-n+m∣≡m         : ∣ n - n + m ∣ ≡ m
  ∣n+m-n+o∣≡∣m-o|   : ∣ n + m - n + o ∣ ≡ ∣ m - o ∣
  n∸m≤∣n-m∣         : n ∸ m ≤ ∣ n - m ∣
  ∣n-m∣≤n⊔m         : ∣ n - m ∣ ≤ n ⊔ m
  ∣-∣-comm          : Commutative ∣_-_∣
  ∣n-m∣≡[n∸m]∨[m∸n] : (∣ n - m ∣ ≡ n ∸ m) ⊎ (∣ n - m ∣ ≡ m ∸ n)
  *-distribˡ-∣-∣    : _*_ DistributesOverˡ ∣_-_∣
  *-distribʳ-∣-∣    : _*_ DistributesOverʳ ∣_-_∣
  *-distrib-∣-∣     : _*_ DistributesOver ∣_-_∣
  ```

* Added new function to `Data.String.Base`:
  ```agda
  fromList⁺ : List⁺ Char → String
  ```

* Added new functions to `Data.Sum`:
  ```agda
  map₁ : (A → B) → A ⊎ C → B ⊎ C
  map₂ : (B → C) → A ⊎ B → A ⊎ C
  ```

* Added new functions in `Data.Table.Base`:
  ```agda
  remove  : Fin (suc n) → Table A (suc n) → Table A n
  fromVec : Vec A n → Table A n
  toVec   : Table A n → Vec A n
  ```

* Added new proofs in `Data.Table.Properties`:
  ```agda
  select-lookup  : lookup (select x i t) i ≡ lookup t i
  select-remove  : remove i (select x i t) ≗ replicate {n} x
  remove-permute : remove (π ⟨$⟩ˡ i) (permute π t) ≗ permute (Perm.remove (π ⟨$⟩ˡ i) π) (remove i t)
  ```

* Added new functions to `Data.Vec.Categorical`:
  ```agda
  sequenceA : RawApplicative F → Vec (F A) n → F (Vec A n)
  mapA      : RawApplicative F → (A → F B) → Vec A n → F (Vec B n)
  forA      : RawApplicative F → Vec A n → (A → F B) → F (Vec B n)
  sequenceM : RawMonad M → Vec (M A) n → M (Vec A n)
  mapM      : RawMonad M → (A → M B) → Vec A n → M (Vec B n)
  forM      : RawMonad M → Vec A n → (A → M B) → M (Vec B n)
  ```

* Added new proofs to `Data.Vec.Properties.All`:
  ```agda
  toList⁺   : All P (toList xs) → All P xs
  toList⁻   : All P xs → All P (toList xs)

  fromList⁺ : All P xs → All P (fromList xs)
  fromList⁻ : All P (fromList xs) → All P xs
  ```

* Added new proofs to `Data.Vec.Membership.Propositional.Properties`:
  ```agda
  ∈-lookup    : lookup i xs ∈ xs

  ∈-toList⁻   : v ∈ toList xs   → v ∈ xs
  ∈-fromList⁻ : v ∈ fromList xs → v ∈ xs
  ```

* Added new proof to `Data.Vec.Properties`:
  ```agda
  lookup-zipWith : lookup i (zipWith f xs ys) ≡ f (lookup i xs) (lookup i ys)
  ```

* Added new proofs to `Data.Vec.Relation.Pointwise.Inductive`:
  ```agda
  tabulate⁺ : (∀ i → f i ~ g i) → Pointwise _~_ (tabulate f) (tabulate g)
  tabulate⁻ : Pointwise _~_ (tabulate f) (tabulate g) → (∀ i → f i ~ g i)
  ```

* Added new type to `Foreign.Haskell`:
  ```agda
  Pair : (A : Set ℓ) (B : Set ℓ′) : Set (ℓ ⊔ ℓ′)
  ```

* Added new function to `Function`:
  ```agda
  typeOf : {A : Set a} → A → Set a
  ```

* Added new functions to `Function.Related`:
  ```agda
  isEquivalence : IsEquivalence (Related ⌊ k ⌋)
  ↔-isPreorder  : IsPreorder _↔_ (Related k)
  ```

* Added new result to `Function.Related.TypeIsomorphisms`:
  ```agda
  ×-comm                    : (A × B) ↔ (B × A)
  ×-identityˡ               : LeftIdentity _↔_ (Lift ℓ ⊤) _×_
  ×-identityʳ               : RightIdentity _↔_ (Lift ℓ ⊤) _×_
  ×-identity                : Identity _↔_ (Lift ℓ ⊤) _×_
  ×-zeroˡ                   : LeftZero _↔_ (Lift ℓ ⊥) _×_
  ×-zeroʳ                   : RightZero _↔_ (Lift ℓ ⊥) _×_
  ×-zero                    : Zero _↔_ (Lift ℓ ⊥) _×_
  ⊎-assoc                   : Associative _↔_ _⊎_
  ⊎-comm                    : (A ⊎ B) ↔ (B ⊎ A)
  ⊎-identityˡ               : LeftIdentity _↔_ (Lift ℓ ⊥) _⊎_
  ⊎-identityʳ               : RightIdentity _↔_ (Lift ℓ ⊥) _⊎_
  ⊎-identity                : Identity _↔_ (Lift ℓ ⊥) _⊎_
  ×-distribˡ-⊎              : _DistributesOverˡ_ _↔_ _×_ _⊎_
  ×-distribʳ-⊎              : _DistributesOverʳ_ _↔_ _×_ _⊎_
  ×-distrib-⊎               : _DistributesOver_ _↔_ _×_ _⊎_
  ×-isSemigroup             : IsSemigroup (Related ⌊ k ⌋) _×_
  ×-semigroup               : Symmetric-kind → Level → Semigroup _ _
  ×-isMonoid                : IsMonoid (Related ⌊ k ⌋) _×_ (Lift ℓ ⊤)
  ×-monoid                  : Symmetric-kind → Level → Monoid _ _
  ×-isCommutativeMonoid     : IsCommutativeMonoid (Related ⌊ k ⌋) _×_ (Lift ℓ ⊤)
  ×-commutativeMonoid       : Symmetric-kind → Level → CommutativeMonoid _ _
  ⊎-isSemigroup             : IsSemigroup (Related ⌊ k ⌋) _⊎_
  ⊎-semigroup               : Symmetric-kind → Level → Semigroup _ _
  ⊎-isMonoid                : IsMonoid (Related ⌊ k ⌋) _⊎_ (Lift ℓ ⊥)
  ⊎-monoid                  : Symmetric-kind → Level → Monoid _ _
  ⊎-isCommutativeMonoid     : IsCommutativeMonoid (Related ⌊ k ⌋) _⊎_ (Lift ℓ ⊥)
  ⊎-commutativeMonoid       : Symmetric-kind → Level → CommutativeMonoid _ _
  ×-⊎-isCommutativeSemiring : IsCommutativeSemiring (Related ⌊ k ⌋) _⊎_ _×_ (Lift ℓ ⊥) (Lift ℓ ⊤)
  ```

* Added new type and function to `Function.Bijection`:
  ```agda
  From ⤖ To = Bijection (P.setoid From) (P.setoid To)

  bijection : (∀ {x y} → to x ≡ to y → x ≡ y) → (∀ x → to (from x) ≡ x) → From ⤖ To
  ```

* Added new function to `Function.Injection`:
  ```agda
  injection : (∀ {x y} → to x ≡ to y → x ≡ y) → From ↣ To
  ```

* Added new function to `Function.Inverse`:
  ```agda
  inverse : (∀ x → from (to x) ≡ x) → (∀ x → to (from x) ≡ x) → From ↔ To
  ```

* Added new function to `Function.LeftInverse`:
  ```agda
  leftInverse : (∀ x → from (to x) ≡ x) → From ↞ To
  ```

* Added new proofs to `Function.Related`:
  ```agda
  K-refl       : Reflexive (Related k)
  K-reflexive  : _≡_ ⇒ Related k
  K-trans      : Trans (Related k) (Related k) (Related k)
  K-isPreorder : IsPreorder _↔_ (Related k)

  SK-sym           : Sym (Related ⌊ k ⌋) (Related ⌊ k ⌋)
  SK-isEquivalence : IsEquivalence (Related ⌊ k ⌋)
  ```

* Added new proofs to `Function.Related.TypeIsomorphisms`:
  ```agda
  ×-≡×≡↔≡,≡ : (x ≡ proj₁ p × y ≡ proj₂ p) ↔ (x , y) ≡ p
  ×-comm    : (A × B) ↔ (B × A)
  ```

* Added new function to `Function.Surjection`:
  ```agda
  surjection : (∀ x → to (from x) ≡ x) → From ↠ To
  ```

* Added new synonym to `Level`:
  ```agda
  0ℓ = zero
  ```

* Added new module `Level.Literals` with functions:
  ```agda
  _ℕ+_   : Nat → Level → Level
  #_     : Nat → Level
  Levelℕ : Number Level
  ```

* Added new proofs to record `IsStrictPartialOrder` in `Relation.Binary`:
  ```agda
  <-respʳ-≈ : _<_ Respectsʳ _≈_
  <-respˡ-≈ : _<_ Respectsˡ _≈_
  ```

* Added new functions and records to `Relation.Binary.Indexed.Heterogeneous`:
  ```agda
  record IsIndexedPreorder  (_≈_ : Rel A ℓ₁) (_∼_ : Rel A ℓ₂) : Set (i ⊔ a ⊔ ℓ₁ ⊔ ℓ₂)
  record IndexedPreorder {i} (I : Set i) c ℓ₁ ℓ₂ : Set (suc (i ⊔ c ⊔ ℓ₁ ⊔ ℓ₂))
  ```

* Added new proofs to `Relation.Binary.Indexed.Heterogeneous.Construction.At`:
  ```agda
  isEquivalence : IsIndexedEquivalence A _≈_  → (i : I) → B.IsEquivalence (_≈_ {i})
  isPreorder    : IsIndexedPreorder A _≈_ _∼_ → (i : I) → B.IsPreorder (_≈_ {i}) _∼_
  setoid        : IndexedSetoid I a ℓ → I → B.Setoid a ℓ
  preorder      : IndexedPreorder I a ℓ₁ ℓ₂ → I → B.Preorder a ℓ₁ ℓ₂
  ```

* Added new proofs to `Relation.Binary.Indexed.Heterogeneous.Construction.Trivial`:
  ```agda
  isIndexedEquivalence : IsEquivalence _≈_ → IsIndexedEquivalence (λ (_ : I) → A) _≈_
  isIndexedPreorder    : IsPreorder _≈_ _∼_ → IsIndexedPreorder (λ (_ : I) → A) _≈_ _∼_
  indexedSetoid        : Setoid a ℓ → ∀ {I} → IndexedSetoid I a ℓ
  indexedPreorder      : Preorder a ℓ₁ ℓ₂ → ∀ {I} → IndexedPreorder I a ℓ₁ ℓ₂
  ```

* Added new types, functions and records to `Relation.Binary.Indexed.Homogeneous`:
  ```agda
  Implies _∼₁_ _∼₂_      = ∀ {i} → _∼₁_ B.⇒ (_∼₂_ {i})
  Antisymmetric _≈_ _∼_  = ∀ {i} → B.Antisymmetric _≈_ (_∼_ {i})
  Decidable _∼_          = ∀ {i} → B.Decidable (_∼_ {i})
  Respects P _∼_         = ∀ {i} {x y : A i} → x ∼ y → P x → P y
  Respectsˡ P _∼_        = ∀ {i} {x y z : A i} → x ∼ y → P x z → P y z
  Respectsʳ P _∼_        = ∀ {i} {x y z : A i} → x ∼ y → P z x → P z y
  Respects₂ P _∼_        = (Respectsʳ P _∼_) × (Respectsˡ P _∼_)
  Lift _∼_ x y           = ∀ i → x i ∼ y i

  record IsIndexedEquivalence  (_≈ᵢ_ : Rel A ℓ)                   : Set (i ⊔ a ⊔ ℓ)
  record IsIndexedPreorder     (_≈ᵢ_ : Rel A ℓ₁) (_∼ᵢ_ : Rel A ℓ₂) : Set (i ⊔ a ⊔ ℓ₁ ⊔ ℓ₂)
  record IsIndexedPartialOrder (_≈ᵢ_ : Rel A ℓ₁) (_≤ᵢ_ : Rel A ℓ₂) : Set (i ⊔ a ⊔ ℓ₁ ⊔ ℓ₂)

  record IndexedSetoid   {i} (I : Set i) c ℓ     : Set (suc (i ⊔ c ⊔ ℓ))
  record IndexedPreorder {i} (I : Set i) c ℓ₁ ℓ₂ : Set (suc (i ⊔ c ⊔ ℓ₁ ⊔ ℓ₂))
  record IndexedPoset    {i} (I : Set i) c ℓ₁ ℓ₂ : Set (suc (i ⊔ c ⊔ ℓ₁ ⊔ ℓ₂))
  ```

* Added new types, records and proofs to `Relation.Binary.Lattice`:
  ```agda
  Exponential _≤_ _∧_ _⇨_ = ∀ w x y → ((w ∧ x) ≤ y → w ≤ (x ⇨ y)) × (w ≤ (x ⇨ y) → (w ∧ x) ≤ y)

  IsJoinSemilattice.x≤x∨y      : x ≤ x ∨ y
  IsJoinSemilattice.y≤x∨y      : y ≤ x ∨ y
  IsJoinSemilattice.∨-least    : x ≤ z → y ≤ z → x ∨ y ≤ z

  IsMeetSemilattice.x∧y≤x      : x ∧ y ≤ x
  IsMeetSemilattice.x∧y≤y      : x ∧ y ≤ y
  IsMeetSemilattice.∧-greatest : x ≤ y → x ≤ z → x ≤ y ∧ z

  record IsDistributiveLattice _≈_ _≤_ _∨_ _∧_
  record IsHeytingAlgebra      _≈_ _≤_ _∨_ _∧_ _⇨_ ⊤ ⊥
  record IsBooleanAlgebra      _≈_ _≤_ _∨_ _∧_ ¬_ ⊤ ⊥

  record DistributiveLattice c ℓ₁ ℓ₂
  record HeytingAlgebra      c ℓ₁ ℓ₂
  record BooleanAlgebra      c ℓ₁ ℓ₂
  ```

* Added new proofs to `Relation.Binary.NonStrictToStrict`:
  ```agda
  <⇒≤ : _<_ ⇒ _≤_
  ```

* Added new proofs to `Relation.Binary.PropositionalEquality`:
  ```agda
  respˡ : ∼ Respectsˡ _≡_
  respʳ : ∼ Respectsʳ _≡_
  ```

* Added new proofs to `Relation.Binary.StrictToNonStrict`:
  ```agda
  <⇒≤ : _<_ ⇒ _≤_

  ≤-respʳ-≈ : Transitive _≈_ → _<_ Respectsʳ _≈_ → _≤_ Respectsʳ _≈_
  ≤-respˡ-≈ : Symmetric _≈_ → Transitive _≈_ → _<_ Respectsˡ _≈_ → _≤_ Respectsˡ _≈_

  <-≤-trans : Transitive _<_ → _<_ Respectsʳ _≈_ → Trans _<_ _≤_ _<_
  ≤-<-trans : Symmetric _≈_ → Transitive _<_ → _<_ Respectsˡ _≈_ → Trans _≤_ _<_ _<_
  ```

* Added the following types in `Relation.Unary`:
  ```agda
  Satisfiable P = ∃ λ x → x ∈ P
  IUniversal P  = ∀ {x} → x ∈ P
  ```

* Added the following proofs in `Relation.Unary.Properties`:
  ```agda
  ∅? : Decidable ∅
  U? : Decidable U
  ```
