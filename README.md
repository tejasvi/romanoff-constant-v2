# New lower bounds for Romanoff's constant (version 2)

Programs and data for the paper *New lower bounds for Romanoff's constant* by Tejasvi Singh Tomar
(doi:10.5281/zenodo.23548700). The programs, the data and a proof of both bounds in Lean 4 are archived at doi:10.5281/zenodo.23548710.

Version 2 adds the Lean proof in `lean/`. The checking program, the certificates, the data and the logs of version 1
(doi:10.5281/zenodo.23539874) are unchanged.

## Results

Let `d` be the lower density of the set of integers `p + 2^a` with `p` prime and `a >= 1`. The paper proves

    d >= 0.109406                                                                  (Theorem 1.1)
    d >= 0.117953, assuming a bound deduced from Lichtman's Theorems 1.2 and 1.7   (Theorem 1.2)

The best lower bound in the literature was 0.10788, from the method of Elsholtz and Schlage-Puchta (Math. Z. 288 (2018),
end of p. 721; recomputed by Johnston and Trudgian, arXiv:2605.17825v2). Theorem 1.2 assumes the bound of Proposition 2
of Johnston and Trudgian, which they deduce from Theorems 1.2 and 1.7 of J. D. Lichtman, arXiv:2309.08522. Both papers
are preprints. Theorem 1.1 uses neither.

## Method

The integers are split into residue classes modulo `l = 3*5*7*11*13*17*31*41*241`. In each class the second moment of
the number of representations is bounded with the per-class sieve bound of Elsholtz and Schlage-Puchta, whose constant 8
is four times the Hardy-Littlewood prediction. For Theorem 1.2 the total over all classes is also bounded with the constant
6.7814 of Johnston and Trudgian. A linear program over the distribution of the pair counts among the classes gives the
bound, and a Lagrangian certificate (one multiplier per difference class, Section 4 of the paper) bounds its minimum from
below. For Theorem 1.1 all multipliers are zero, and the constant 6.7814 plays no role. The singular series is bounded
with the prime factors of `2^t - 1` for `t <= 1200` (Section 5 of the paper).

## Contents

| Path | Content |
|---|---|
| `check/check_r45a5_rel2_3cf71761.py` | the checking program |
| `check/cert_p120b_uncond_af1c16be.json` | the certificate of Theorem 1.1 (all multipliers zero) |
| `check/cert_p120b_v3_6fd62e90.json` | the certificate of Theorem 1.2 |
| `check/fac300_41e4cdd6.json` | the complete factorizations of `Phi_t(2)` for `t <= 300` |
| `check/fac1200_fdb_af082adc.json` | factorizations of `Phi_t(2)` for `301 <= t <= 1200` |
| `logs/` | the output of the checking runs |
| `reproduce/` | the programs that produced the data and the certificates (not needed for checking) |
| `lean/` | the proof of both theorems in Lean 4 (see `lean/README.md`) |
| `SHA256SUMS` | the SHA-256 checksums of all other files |

## Checking

The checker needs Python 3 with python-flint (tested with 0.9.0) and numpy. In the directory `check`:

    python3 -I check_r45a5_rel2_3cf71761.py fac300_41e4cdd6.json cert_p120b_uncond_af1c16be.json 3000000 fac1200_fdb_af082adc.json 1200 --claim 0.109406
    python3 -I check_r45a5_rel2_3cf71761.py fac300_41e4cdd6.json cert_p120b_v3_6fd62e90.json 3000000 fac1200_fdb_af082adc.json 1200 --claim 0.117953

The two runs take about 24 and 29 minutes on one core. Each prints the certified value and the value rounded down, and
ends with `PASS` and exit code 0 if the certified value is at least the claim. `3000000` is the bound at which the dynamic
program over the orders stops a branch, and `1200` is the largest order of 2 modulo the primes of class (b) (Section 5
of the paper). Without the arguments `fac1200_fdb_af082adc.json 1200` the checker leaves out class (b) and certifies a
smaller value.

The checker recomputes everything from the factorization files and the certificate:
- It checks by multiplication that the factorizations for `t <= 300` are complete and proves every factor prime. For
  `301 <= t <= 1200` it checks the products and proves the primality and the order `t` of every factor below `10^100`
  that is coprime to `t`. It does not use the other factors.
- It computes the orders exactly, bounds the singular series in ball arithmetic and, for class (b), in floating point with
  outward rounding and a relative margin of `1e-9`.
- It builds the residue patterns and their rotation orbits and evaluates the Lagrangian bound in exact rational arithmetic.

The certificate contains the prime factors of `l`, the multipliers and the external constants: the per-class constant 8,
the total constant 6.7814, `C0 < 0.66016181584686958` for the twin prime constant `C0`,
`0.693147180559945309 < log 2 < 0.693147180559945310` and `R0 < 1.93656` for the sum (2.3) of the paper. In the
certificate of Theorem 1.1 the total constant is 8; it has no effect there, since all multipliers are zero. The checker
requires:
- the per-class constant to be at least 8 and the total constant at least 6.7814, both at most 100;
- `0.66016181584686958 <= C0_up <= 1`, `0.6931471805599453 <= log2_low <= 0.693147180559945309`,
  `0.693147180559945310 <= log2_up <= 0.6931471805599454` and `1.93656 <= R0_up <= 2`;
- the prime factors of `l` to be distinct odd primes, and the primes in each factorization to be distinct;
- the multipliers to be nonnegative.

It prints all parameters in its first line, and as a precaution it refuses to run under `python -O`.

## Formal proof in Lean

The directory `lean/` contains a proof of Theorems 1.1 and 1.2 in Lean 4 (version v4.35.0-rc3) with Mathlib (commit
c55e6e786f49): the theorems `Romanoff.romanoff_U2` and `Romanoff.romanoff_v3` in `lean/Romanoff/Theorems.lean`. The
analytic inputs of Section 2 of the paper are their hypotheses: the per-class bound (2.1) for the modulus `l`, the prime
number theorem in progressions modulo `l` and the bound (2.3) for `R0`, stated for its finite partial sums, and for
Theorem 1.2 also the bound (2.2) for the total. Pintz's inequality (Lemma 2.1) is proved. The checks of the data run as
compiled code through `native_decide`, so `#print axioms` lists the three standard axioms and four auxiliary axioms for
each theorem (`lean/results/axioms_shipped.txt`). `lean/README.md` describes the files and gives the build commands.

## Reproduction

The programs in `reproduce/` produced the data and the certificates. Their names, like those of the data files, end with
the first eight hex digits of the file's SHA-256. To run them, copy them to names without this suffix
(`dual_opt.py` imports `patterns_fast`). They need python-flint, numpy and highspy.
- `fetch_fdb.sh` and `fdb_to_phi.py` fetch the factorizations of `2^t - 1` from factordb and convert them to
  factorizations of `Phi_t(2)`; `factor_full.py` factors `Phi_t(2)` for `t <= 300` with FLINT; `verify_factors.py` checks
  the files.
- `patterns_fast.py` computes the residue patterns and their orbits, and `dual_opt.py` computes the multipliers from the
  parameter file `params_p120b_e724da74.json` and the singular-series bounds `tails_p120b_v5_c073e0de.json` (which the
  checker recomputes).
- `cert_build3.py` solves the linear program in floating point with HiGHS, as a cross-check of the multipliers.
- `sensitivity.py` evaluates the Lagrangian bound in floating point, with one common multiplier, for other values of the
  two constants; `sens_p120b_a87b7f38.log` is its output for the modulus of the paper. These values are not certified, and
  the checker refuses most of these constants.

## Notes

- The `note` fields inside the two certificates were written for the earlier checker `check_r45a5_rel_ab861be2.py` and
  name the certificates without the hash suffix. The checker here supersedes that one: it adds the input checks listed
  above and certifies the same values.
- The notes call the inputs (I1) to (I5): (I1) is the per-class bound (2.1) of the paper, (I2) the total bound (2.2),
  (I3) the prime number theorem in progressions, (I4) Lemma 2.1 and (I5) the bound (2.3) for `R0`.
- The parameter notes attribute `R0 < 1.93656` to Khalfalah and Pintz. The bound follows from Pintz's
  `C0 R0 < 1.2784421041` (Acta Math. Hungar. 112 (2006), Lemma 2'') and `C0 > 0.6601618158`, as quoted by Platt and
  Trudgian (J. Number Theory 153 (2015), p. 55).

## License

Apache License 2.0, see `LICENSE`.
