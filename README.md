# Blockchain-based-Intellectual-Property-Marketplace
Use blockchain technology to create a decentralized marketplace for buying, selling, and licensing intellectual property rights.
import hashlib
import time
from uuid import uuid4

class Block:
    def __init__(self, index, transactions, timestamp, previous_hash):
        self.index = index
        self.transactions = transactions
        self.timestamp = timestamp
        self.previous_hash = previous_hash
        self.nonce = 0  # used for mining
        self.hash = self.compute_hash()

    def compute_hash(self):
        block_string = f"{self.index}{self.transactions}{self.timestamp}{self.previous_hash}{self.nonce}"
        return hashlib.sha256(block_string.encode()).hexdigest()

    def mine_block(self, difficulty):
        while self.hash[:difficulty] != '0' * difficulty:
            self.nonce += 1
            self.hash = self.compute_hash()
        print(f"Block mined: {self.hash}")

class Blockchain:
    def __init__(self):
        self.unconfirmed_transactions = []
        self.chain = []
        self.create_genesis_block()
        self.difficulty = 2

    def create_genesis_block(self):
        genesis_block = Block(0, [], time.time(), "0")
        genesis_block.mine_block(self.difficulty)
        self.chain.append(genesis_block)

    def add_new_transaction(self, transaction):
        self.unconfirmed_transactions.append(transaction)

    def mine_unconfirmed_transactions(self):
        if not self.unconfirmed_transactions:
            return False
        last_block = self.chain[-1]
        new_block = Block(index=last_block.index + 1,
                          transactions=self.unconfirmed_transactions,
                          timestamp=time.time(),
                          previous_hash=last_block.hash)
        new_block.mine_block(self.difficulty)
        self.chain.append(new_block)
        self.unconfirmed_transactions = []
        return True

    def display_chain(self):
        for block in self.chain:
            print(vars(block))

class SmartContract:
    def __init__(self):
        self.ip_registry = {}

    def register_ip(self, owner, ip_details):
        ip_id = str(uuid4())
        self.ip_registry[ip_id] = {'owner': owner, 'details': ip_details}
        return ip_id

    def transfer_ownership(self, ip_id, new_owner):
        if ip_id in self.ip_registry:
            self.ip_registry[ip_id]['owner'] = new_owner
            return True
        return False

# Example Usage
blockchain = Blockchain()
smart_contract = SmartContract()

# Registering an IP and simulating a transaction
ip_id = smart_contract.register_ip('Alice', {'name': 'Cool Invention', 'type': 'Patent'})
transaction = {'type': 'sell', 'ip_id': ip_id, 'from': 'Alice', 'to': 'Bob'}

blockchain.add_new_transaction(transaction)
blockchain.mine_unconfirmed_transactions()

blockchain.display_chain()

# Transferring ownership through smart contract
smart_contract.transfer_ownership(ip_id, 'Bob')
print(smart_contract.ip_registry)
