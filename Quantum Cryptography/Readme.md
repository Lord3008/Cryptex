

from qiskit import QuantumCircuit, Aer, execute

from qiskit.visualization import plot_histogram

import numpy as np
import random

num_qubits = 10

alice_bits = np.random.randint(2, size=num_qubits)
alice_bases = np.random.randint(2, size=num_qubits)
bob_bases = np.random.randint(2, size=num_qubits)

qc = QuantumCircuit(num_qubits, num_qubits)

for i in range(num_qubits):
    if alice_bases[i] == 0:
        if alice_bits[i] == 1:
            qc.x(i)
    else:
        if alice_bits[i] == 1:
            qc.x(i)
        qc.h(i)

for i in range(num_qubits):
    if bob_bases[i] == 1:
        qc.h(i)
    qc.measure(i, i)

simulator = Aer.get_backend('qasm_simulator')
result = execute(qc, simulator, shots=1).result()
counts = result.get_counts(qc)

bob_results = [int(bit) for bit in list(counts.keys())[0]]

key = []
for i in range(num_qubits):
    if alice_bases[i] == bob_bases[i]:
        key.append(alice_bits[i])

print("Alice's bits:      ", alice_bits)
print("Alice's bases:     ", alice_bases)
print("Bob's bases:       ", bob_bases)
print("Bob's measurements:", bob_results)
print("Shared key:        ", key)

qc.draw('mpl')

