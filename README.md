# -AI-
スマートシティは、エッジAIとブロックチェーン技術を活用して、都市データの安全な共有と分析を実現し、都市インフラの最適化と住民サービスの向上を図ります。
import hashlib
import json
from time import time
from uuid import uuid4
from flask import Flask, jsonify, request

# Part 1: Simulating Edge AI data collection
def collect_data():
    """
    Simulate collecting urban data with Edge AI. This function generates
    sample data representing a simplified version of what Edge AI might collect.
    """
    # Example data: traffic patterns and energy usage
    data = {
        "traffic": {"cars": 100, "buses": 10},
        "energy_usage": {"residential": 5000, "commercial": 8000}
    }
    return data

# Part 2: A very basic blockchain to securely share data
class Blockchain:
    def __init__(self):
        self.chain = []
        self.current_data = []
        self.new_block(previous_hash='1', proof=100)

    def new_block(self, proof, previous_hash=None):
        """
        Create a new Block in the Blockchain
        """
        block = {
            'index': len(self.chain) + 1,
            'timestamp': time(),
            'data': self.current_data,
            'proof': proof,
            'previous_hash': previous_hash or self.hash(self.chain[-1]),
        }
        self.current_data = []
        self.chain.append(block)
        return block

    def new_data(self, data):
        """
        Adds a new data to the list of data
        """
        self.current_data.append(data)
        return self.last_block['index'] + 1

    @staticmethod
    def hash(block):
        """
        Creates a SHA-256 hash of a Block
        """
        block_string = json.dumps(block, sort_keys=True).encode()
        return hashlib.sha256(block_string).hexdigest()

    @property
    def last_block(self):
        return self.chain[-1]

    # Simple Proof of Work Algorithm here if needed

app = Flask(__name__)

# Generate a globally unique address for this node
node_identifier = str(uuid4()).replace('-', '')

# Instantiate the Blockchain
blockchain = Blockchain()

@app.route('/mine', methods=['GET'])
def mine():
    # We run the proof of work algorithm to get the next proof...
    # This is simplified for the demo
    last_block = blockchain.last_block
    last_proof = last_block['proof']
    proof = last_proof + 1  # This should be replaced with a real proof of work

    # We must receive a reward for finding the proof.
    blockchain.new_data({
        'sender': "0",
        'recipient': node_identifier,
        'data': collect_data(),
    })

    # Forge the new Block by adding it to the chain
    previous_hash = blockchain.hash(last_block)
    block = blockchain.new_block(proof, previous_hash)

    response = {
        'message': "New Block Forged",
        'index': block['index'],
        'data': block['data'],
        'proof': proof,
        'previous_hash': previous_hash,
    }
    return jsonify(response), 200

@app.route('/chain', methods=['GET'])
def full_chain():
    response = {
        'chain': blockchain.chain,
        'length': len(blockchain.chain),
    }
    return jsonify(response), 200

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
