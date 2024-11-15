import {
  Connection,
  Keypair,
  SystemProgram,
  Transaction,
  clusterApiUrl,
  sendAndConfirmTransaction,
} from "@solana/web3.js";
import {
  MINT_SIZE,
  TOKEN_2022_PROGRAM_ID,
  createInitializeMint2Instruction,
  getMinimumBalanceForRentExemptMint,
} from "@solana/spl-token";
 
const wallet = pg.wallet;
const connection = new Connection(clusterApiUrl("devnet"), "confirmed");
 
// Generate keypair to use as address of mint account
const mint = new Keypair();
 
// Calculate minimum lamports for space required by mint account
const rentLamports = await getMinimumBalanceForRentExemptMint(connection);
 
// Instruction to create new account with space for new mint account
const createAccountInstruction = SystemProgram.createAccount({
  fromPubkey: wallet.publicKey,
  newAccountPubkey: mint.publicKey,
  space: MINT_SIZE,
  lamports: rentLamports,
  programId: TOKEN_2022_PROGRAM_ID,
});
 
// Instruction to initialize mint account
const initializeMintInstruction = createInitializeMint2Instruction(
  mint.publicKey,
  2, // decimals
  wallet.publicKey, // mint authority
  wallet.publicKey, // freeze authority
  TOKEN_2022_PROGRAM_ID,
);
 
// Build transaction with instructions to create new account and initialize mint account
const transaction = new Transaction().add(
  createAccountInstruction,
  initializeMintInstruction,
);
 
const transactionSignature = await sendAndConfirmTransaction(
  connection,
  transaction,
  [
    wallet.keypair, // payer
    mint, // mint address keypair
  ],
);
 
console.log(
  "\nTransaction Signature:",
  `https://solana.fm/tx/${transactionSignature}?cluster=devnet-solana`,
);
 
console.log(
  "\nMint Account:",
  `https://solana.fm/address/${mint.publicKey}?cluster=devnet-solana`,
);# yoyo
