---
title: Runtime Execution
---

The execution of the Substrate runtime is orchestrated by the Executive pallet in the Framework for Runtime Aggregation of Modularised Entities (FRAME).

Unlike the other pallets in the FRAME, this is not a pallet per se, but just a normal Rust module which calls into the various pallets included in your blockchain.

The Executive pallet exposes the `execute_block` function that:

* [Initializes the block](#initializing-a-block).

* [Executes extrinsics](#executing-extrinsics).

* [Finalizes the block](#finalizing-a-block).

## Validating Transactions

Before block execution begins, signed transaction are checked for validity. This doesn't execute any side-effects; it merely checks whether the transaction would panic if it were included or not. Thus, changes made to storage are discarded.

## Executing a Block

Once there is a queue of valid transactions, the Executive pallet begins to execute the block.

### Initializing a Block

To initialize a block, the System pallet and all other included pallets have their `on_initialize` function called which executes any business logic defined by those pallets to take place before transactions are executed. The pallets are executed in the order which they are defined in the `construct_runtime!` macro, but with the System pallet always executing first.

Then, initial checks take place where the parent hash in the block header is verified to be correct and the extrinsics trie root actually represents the extrinsics.

### Executing Extrinsics

After the block has been initialized, each valid extrinsic is executed in order of transaction priority. Extrinsics must not cause a panic in the runtime logic or else the system becomes vulnerable to attacks where users can trigger computational execution without any punishment.

### Finalizing a Block

After all queued extrinsics have been executed, the Executive pallet calls into each pallet's `on_finalize` function to perform any final business logic which should take place at the end of the block. The pallets are again executed in the order which they are defined in the `construct_runtime!` macro, but in this case, the System pallet finalizes last.

Then, final checks take place where the digest and storage root in the block header match what was calculated.

## Next Steps

### Learn More

* Look at the documentation for [Declaring a Pallet](development/module/declaration.md) to see how you can define `on_initialize` and `on_finalize` logic for your pallet.

* Learn how how you can simulate the orchestration of the Executive pallet in your [runtime tests](development/module/tests.md).

### Examples

TODO

### References

* Visit the reference docs for the [Executive module](https://substrate.dev/rustdocs/master/pallet_executive/index.html).

* Visit the reference docs for the [`decl_event!` macro](https://substrate.dev/rustdocs/master/palette_support/macro.decl_event.html).

* Visit the reference docs for the [`decl_storage!` macro](https://substrate.dev/rustdocs/master/palette_support/macro.decl_storage.html).

* Visit the reference docs for the [`construct_runtime!` macro](https://substrate.dev/rustdocs/master/palette_support/macro.construct_runtime.html).
