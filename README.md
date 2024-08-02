Blockchain Class: Implements a simple blockchain with methods for creating blocks, adding transactions, proof of work, and chain validation.
TokenEconomy Class: Extends the Blockchain class to include basic token economy functionalities such as account creation, token transfer, and balance checking.
This code is a simplified representation and lacks many real-world features such as network communication, advanced consensus mechanisms, and security measures. For a production-grade system, you would need to consider these aspects carefully.

import hashlib
import json
from time import time

class Blockchain:
    def __init__(self):
        self.chain = []
        self.pending_transactions = []

        # Create the genesis block
        self.new_block(previous_hash='1', proof=100)

    def new_block(self, proof, previous_hash=None):
        block = {
            'index': len(self.chain) + 1,
            'timestamp': time(),
            'transactions': self.pending_transactions,
            'proof': proof,
            'previous_hash': previous_hash or self.hash(self.chain[-1]),
        }

        # Reset the current list of transactions
        self.pending_transactions = []
        self.chain.append(block)
        return block

    def new_transaction(self, sender, recipient, amount):
        self.pending_transactions.append({
            'sender': sender,
            'recipient': recipient,
            'amount': amount,
        })

        return self.last_block['index'] + 1

    @property
    def last_block(self):
        return self.chain[-1]

    @staticmethod
    def hash(block):
        block_string = json.dumps(block, sort_keys=True).encode()
        return hashlib.sha256(block_string).hexdigest()

    def proof_of_work(self, last_proof):
        proof = 0
        while self.valid_proof(last_proof, proof) is False:
            proof += 1
        return proof

    @staticmethod
    def valid_proof(last_proof, proof):
        guess = f'{last_proof}{proof}'.encode()
        guess_hash = hashlib.sha256(guess).hexdigest()
        return guess_hash[:4] == "0000"

    def is_valid_chain(self, chain):
        last_block = chain[0]
        current_index = 1

        while current_index < len(chain):
            block = chain[current_index]
            if block['previous_hash'] != self.hash(last_block):
                return False

            if not self.valid_proof(last_block['proof'], block['proof']):
                return False

            last_block = block
            current_index += 1

        return True
        
class TokenEconomy(Blockchain):
    def __init__(self):
        super().__init__()
        self.accounts = {}

    def create_account(self, address):
        if address not in self.accounts:
            self.accounts[address] = 100  # Initial token amount
            print(f"Account created for {address} with 100 tokens.")
        else:
            print(f"Account already exists for {address}.")

    def transfer(self, sender, recipient, amount):
        if sender not in self.accounts or recipient not in self.accounts:
            return "Account not found."

        if self.accounts[sender] < amount:
            return "Insufficient balance."

        self.accounts[sender] -= amount
        self.accounts[recipient] += amount
        self.new_transaction(sender, recipient, amount)
        print(f"{amount} tokens transferred from {sender} to {recipient}.")

    def account_balance(self, address):
        return self.accounts.get(address, 0)

# Example Usage
if __name__ == "__main__":
    # Initialize the token economy
    economy = TokenEconomy()

    # Create accounts
    economy.create_account("Abdullah")
    economy.create_account("Sajida")

    # Check initial balances
    print(f"Abdullah balance: {economy.account_balance('Abdullah')}")
    print(f"Sajida balance: {economy.account_balance('Sajida')}")

    # Transfer tokens
    economy.transfer("Abdullah", "Sajida", 50)

    # Check balances after transfer
    print(f"Abdullah balance: {economy.account_balance('Abdullah')}")
    print(f"Sajida balance: {economy.account_balance('Sajida')}")


Account created for Abdullah with 100 tokens.
Account created for Sajida with 100 tokens.
Abdullah balance: 100
Sajida balance: 100
50 tokens transferred from Abdullah to Sajida.
Abdullah balance: 50
Sajida balance: 150


