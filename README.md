# Blockchain-based-Carbon-Credit-Trading
Develop a decentralized platform for trading carbon credits, promoting transparency and accountability in environmental conservation efforts.
import hashlib
import time

class Block:
    def __init__(self, index, transactions, timestamp, previous_hash, nonce=0):
        self.index = index
        self.transactions = transactions
        self.timestamp = timestamp
        self.previous_hash = previous_hash
        self.nonce = nonce
        self.hash = self.compute_hash()

    def compute_hash(self):
        block_string = f"{self.index}{self.transactions}{self.timestamp}{self.previous_hash}{self.nonce}"
        return hashlib.sha256(block_string.encode()).hexdigest()

    def proof_of_work(self, difficulty):
        self.nonce = 0
        while self.compute_hash()[:difficulty] != '0' * difficulty:
            self.nonce += 1
        self.hash = self.compute_hash()

class Blockchain:
    def __init__(self, difficulty=2):
        self.unconfirmed_transactions = []
        self.chain = []
        self.create_genesis_block()
        self.difficulty = difficulty

    def create_genesis_block(self):
        genesis_block = Block(0, [], time.time(), "0")
        genesis_block.proof_of_work(self.difficulty)
        self.chain.append(genesis_block)

    def add_new_transaction(self, transaction):
        self.unconfirmed_transactions.append(transaction)

    def add_block(self, block, proof):
        previous_hash = self.last_block.hash

        if previous_hash != block.previous_hash:
            return False

        if not self.is_valid_proof(block, proof):
            return False

        block.hash = proof
        self.chain.append(block)
        return True

    def is_valid_proof(self, block, block_hash):
        return (block_hash.startswith('0' * self.difficulty) and
                block_hash == block.compute_hash())

    def mine(self):
        if not self.unconfirmed_transactions:
            return False

        last_block = self.last_block

        new_block = Block(index=last_block.index + 1,
                          transactions=self.unconfirmed_transactions,
                          timestamp=time.time(),
                          previous_hash=last_block.hash)

        new_block.proof_of_work(self.difficulty)
        self.add_block(new_block, new_block.hash)
        self.unconfirmed_transactions = []
        return new_block.index

    @property
    def last_block(self):
        return self.chain[-1]

# Example usage
carbon_credit_blockchain = Blockchain()
carbon_credit_blockchain.add_new_transaction({"from": "Alice", "to": "Bob", "amount": 100})
carbon_credit_blockchain.mine()

print(f"Blockchain: {[block.hash for block in carbon_credit_blockchain.chain]}")
