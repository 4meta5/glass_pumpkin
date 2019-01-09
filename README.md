# Glass Pumpkin
A random number generator for generating large prime numbers, suitable for cryptography.

# Purpose
`glass_pumpkin` is a cryptographically-secure, random number generator, useful for generating large prime numbers.
This library was inspired by [pumpkin](https://github.com/zcdziura/pumpkin) except its meant to be used with rust stable.
It also lowers the 512-bit restriction to 128 bits so these can be generated and used for elliptic curve prime fields.
It exposes the prime testing functions as well.
This crate uses [num-bigint](https://crates.io/crates/num-bigint) instead of `ramp`.

# Installation
Add the following to your `Cargo.toml` file:
```toml
glass-pumpkin = "0.2"
```

# Example
```rust
extern crate glass_pumpkin;

use glass_pumpkin::prime;

fn main() {
    let p = prime::new(1024);
    let q = prime::new(1024);

    let n = p * q;

    println!("{}", n);
}
```

You can also supply `OsRng` and generate primes from that.
```
extern crate glass_pumpkin;
extern crate rand;

use glass_pumpkin::prime;

use rand::rngs::OsRng;
use rand::thread_rng;

fn main() {
    let mut rng = OsRng::new().unwrap();
    let p = prime::from_rng(1024, &mut rng);
    let q = prime::from_rng(1024, &mut rng);

    let n = p * q;
    println!("{}", n);
}
```

# Prime Generation

`Primes` are generated similarly to OpenSSL except it applies some recommendations from the [Prime and Prejudice](https://eprint.iacr.org/2018/749.pdf) paper:

1. Generate a random odd number of a given bit-length.
1. Divide the candidate by the first 2048 prime numbers
1. Test the candidate with Fermat's Theorem.
1. Runs log2(bitlength) + 5 Miller-Rabin tests.

Safe primes require (n-1)/2 also be prime.

# Prime Checking

You can use this crate to check numbers for primality.
```
extern crate glass_pumpkin;
extern crate num_bigint;

use glass_pumpkin::prime;
use glass_pumpkin::safe_prime;
use num_bigint::BigUint;

fn main() {

    if prime::check(BigUint::from(5)) {
        println!("is prime");
    }

    if safe_prime::check(BigUint::from(7)) {
        println!("is safe prime");
    }
}
```
