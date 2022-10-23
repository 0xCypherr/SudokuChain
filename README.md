# SUDOKU CHAIN

#### Disclaimer

This design was made as a completely absurd project.
There are no security guarantees.
On the contrary, there are extremely critical vulnerabilities.
The parameters used are intentionally limited to enable to implement on my personal computer. It is not possible to use this project in real life, as it is possible to pre-calculate the entire blockchain. Please treat it as a concept project and go through without much thought.

## How it works

Modern cryptography relies on difficult questions. Encryptions, digital signatures or cyptographic hash functions are based on a mathematical question that can be solved one-way. In other words, any function that is difficult to compute and easy to verify can be fitted to this system. Currently, in the oldest blockchain, bitcoin proof of work is based on SHA256. Computationally, first 60 bits of the SHA256 digest have to be zero and this probability is 1/(2^60) for random guesses. There is no other way than brute-force and it can be verified in seconds.

On the other hand, Sudoku is a well-known puzzle for most people. In addition it is difficult to solve but very easy to verify just like SHA256 based POWs. Even though 9x9 sudoku can be solved in seconds on personal computers, it can challenge modern hardware requirements with the increase of the size of the sudoku table.

![how-it-works](SudokuChain.png)

## How Do New Sudoku Problems generate automatically?

There are 6,670,903,752,021,072,936,960 possible discrete results in a 9x9 sudoku. The nice thing about Sudoku is that all these results are results of shuffles of rows, columns or 3x3 blocks. Therefore, it is possible to create all possible sudoku's by randomly changing the position of rows, columns or 3x3 blocks. If we can create a specific random number generator seed for each block, we will find a separate sudoku solution for each block.

If some of the certain digits are deleted by the random number generator (with the same seed), a solvable sudoku puzzle will be obtained. Of course, the remaining digits will affect the solving time (mining time) of this sudoku, which is the equilavent of the length of the PoW in bitcoin.

Notice that, size of sudoku also effects the difficulty level. In addition, parallelization is also possible with sudoku solving algorithms. In this implementation, backtracking has been used.

## How generator seed is calculated?

Obiviously, transactions should effect the sudoku problems with avalanche. Therefore, merkle root implemented to sustain relationship with Sudoku and Txs. However, unlike bitcoin Merkle Root will be casting to integer.
Lets assume, you calculated 123 as merkle tree root. The generator seed is sum of merkle tree root and previous block's solution. This solution is consequitive concatication of all numbers in order.

Result of Sudoku

```bash
[[8 4 3 1 9 2 6 5 7]
 [5 2 1 6 8 7 4 9 3]
 [6 9 7 5 3 4 8 2 1]
 [3 1 6 8 7 9 2 4 5]
 [4 5 8 2 1 3 9 7 6]
 [2 7 9 4 5 6 1 3 8]
 [9 6 5 7 4 1 3 8 2]
 [1 8 4 3 2 5 7 6 9]
 [7 3 2 9 6 8 5 1 4]]

Seed: 843192657521687493697534821316879245458213976279456138965741382184325769732968514

```

Generator Seed

```bash
generatorSeed = MerkleRootInteger + PrevSudokuSolution
newSudokuProblem = GenerateSudoku(generatorSeed)
```

## What about verification?

Verification is the easy concept in Sudoku. Just check each block, row and column for each cell. If everything cells returns True, you verified the block. Congrats !!!

## How can you be sure that there will always be one solution?

I can't. But I can take the first solution that works and build the whole chain on it. Do you think there is only one SHA256 digest whose first 60 bits are 0?

## If you can get new sudoku by modifying a standard sudoku, what is this standard sudoku?

The standart sudoku is the following. Nothing special about it. I got the first sudoku that I've seen on google.

```
[4, 8, 3, 9, 2, 1, 6, 5, 7]
[9, 6, 7, 3, 4, 5, 8, 2, 1]
[2, 5, 1, 8, 7, 6, 4, 9, 3]
[5, 4, 8, 1, 3, 2, 9, 7, 6]
[7, 2, 9, 5, 6, 4, 1, 3, 8]
[1, 3, 6, 7, 9, 8, 2, 4, 5]
[3, 7, 2, 6, 8, 9, 5, 1, 4]
[8, 1, 4, 2, 5, 3, 7, 6, 9]
[6, 9, 5, 4, 1, 7, 3, 8, 2]
```

## What about the performance?

For 3x3 Sudoku:

Level = How many cells should you solve?

```
Level 1
Init: 0.00047912697003994254
Solve: 2.520560001357808e-05
Verify: 0.0004077520800274215
Solve/Verify Time Ratio: 0.06181599370843919
Level 10
Init: 0.0004830567799581331
Solve: 0.00022909791996426066
Verify: 0.0004039838199423684
Solve/Verify Time Ratio: 0.5670967713433259
Level 20
Init: 0.00048461789012435477
Solve: 0.0005719544699786638
Verify: 0.0003878338401591463
Solve/Verify Time Ratio: 1.4747410121405709
Level 30
Init: 0.0004728678300307365
Solve: 0.0014725412300140306
Verify: 0.00024136275002092588
Solve/Verify Time Ratio: 6.100946520895883
Level 40
Init: 0.0004764309999154648
Solve: 0.010971392970022862
Verify: 8.518328995705815e-05
Solve/Verify Time Ratio: 128.7974786551877
Level 50
Init: 0.0004760384500150394
Solve: 0.1492421965898575
Verify: 3.139523008940159e-05
Solve/Verify Time Ratio: 4753.658315765575
Level 60
Init: 0.0005038729402258469
Solve: 4.4332764617499745
Verify: 2.8971490028197877e-05
Solve/Verify Time Ratio: 153022.0384742061
```

As expected, for low values, solving is much easier than verifiying. However, it is 3x3 Sudoku for this limited space (Difficulty = 60), 15302200 % of difference between verification and mining is satisfiying.

## How about Digital Signatures?

I am still using the Eliptic Curve Digital Signature Algorithm as standart.
