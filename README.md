This script simulates and visualizes the **Ising Model**, a fundamental model in statistical mechanics, which models magnetism in a lattice of spins. The Ising Model provides insights into phase transitions, where materials shift between magnetic and non-magnetic states. This code employs the **Metropolis-Hastings algorithm**, which is commonly used in Monte Carlo simulations to calculate the model’s energy and magnetization over time.

### Required Libraries
```python
pip install numpy matplotlib
```
- **NumPy**: For efficient numerical operations.
- **Matplotlib**: For plotting the lattice of spins to visualize the magnetic domains.

### Code Walkthrough

#### 1. Importing Libraries
```python
import numpy as np
import matplotlib.pyplot as plt
```
- **NumPy** (`np`): Provides functions for creating and manipulating arrays, which is essential for representing and updating spins on a lattice.
- **Matplotlib** (`plt`): Used for visualizing the spin configuration on the lattice.

#### 2. Ising Model Class
The `IsingModel` class encapsulates the simulation, including the initialization of spins, calculation of energy and magnetization, the Metropolis algorithm for simulation, and plotting the lattice configuration.

```python
class IsingModel:
    def __init__(self, L, T, J=1, H=0):
        self.L = L  # Lattice size
        self.T = T  # Temperature
        self.J = J  # Coupling constant (interaction strength between neighboring spins)
        self.H = H  # External magnetic field
        self.grid = np.random.choice([-1, 1], size=(L, L))  # Initializes a random spin grid
```
- **Attributes**:
  - `L`: Size of the lattice grid (e.g., 20x20).
  - `T`: Temperature, controlling thermal fluctuations.
  - `J`: Coupling constant, representing the energy of interaction between neighboring spins.
  - `H`: External magnetic field strength.
  - `grid`: Randomly initialized lattice of spins (either +1 or -1).

#### 3. Calculating Energy
```python
def energy(self):
    E = 0
    for i in range(self.L):
        for j in range(self.L):
            s = self.grid[i, j]
            E -= self.J * s * (self.grid[(i + 1) % self.L, j] + self.grid[i, (j + 1) % self.L])
            E -= self.H * s  # Include external magnetic field effect
    return E
```
- Computes the total energy of the lattice by summing up contributions from each spin’s interaction with its neighbors.
- **Periodic Boundary Conditions**: Used to model an infinite lattice by connecting the edges, which reduces edge effects in simulations.

#### 4. Calculating Magnetization
```python
def magnetization(self):
    return np.sum(self.grid)
```
- **Magnetization**: Sum of all spins in the lattice, indicating the overall alignment. A high value indicates a strong magnetic order, while a low value suggests disorder.

#### 5. Flipping a Spin
```python
def flip_spin(self, i, j):
    s = self.grid[i, j]
    dE = 2 * self.J * s * (self.grid[(i + 1) % self.L, j] + self.grid[i, (j + 1) % self.L] +
                           self.grid[(i - 1) % self.L, j] + self.grid[i, (j - 1) % self.L]) + 2 * self.H * s
    return dE
```
- **Purpose**: Calculates the energy difference (`dE`) from flipping a spin at a chosen position `(i, j)`.

#### 6. Metropolis Step
```python
def metropolis_step(self):
    for _ in range(self.L**2):
        i, j = np.random.randint(0, self.L, size=2)  # Random spin selection
        dE = self.flip_spin(i, j)
        # Acceptance probability based on energy difference
        if dE < 0 or np.random.rand() < np.exp(-dE / self.T):
            self.grid[i, j] *= -1  # Flip the spin
```
- **Metropolis Algorithm**: Selects random spins, calculates `dE`, and decides if the flip should occur based on the temperature `T`. If `dE < 0`, the flip always occurs (lower energy is favorable); otherwise, it may occur probabilistically based on temperature.

#### 7. Running the Simulation
```python
def simulate(self, steps):
    for step in range(steps):
        self.metropolis_step()
        if step % 100 == 0:
            print(f"Step {step}/{steps}: Magnetization = {self.magnetization()}")
```
- Repeats the `metropolis_step()` for a specified number of steps, simulating the thermal dynamics of the lattice.
- Every 100 steps, the magnetization is printed, tracking the lattice's evolution over time.

#### 8. Plotting the Lattice
```python
def plot_grid(self):
    plt.imshow(self.grid, cmap='coolwarm', interpolation='nearest')
    plt.title(f"Ising Model Configuration at T={self.T}")
    plt.colorbar(label='Spin')
    plt.show()
```
- Visualizes the lattice configuration. Spins are shown as colors (e.g., red and blue), where each color corresponds to a specific spin orientation.

### Main Script Execution
```python
# Parameters
L = 20  # Lattice size
T = 2.5  # Temperature
steps = 10000  # Simulation steps

# Instantiate and run the model
ising_model = IsingModel(L, T)
ising_model.simulate(steps)
ising_model.plot_grid()
```
- **Parameters**:
  - `L=20`: Sets the lattice to a 20x20 grid.
  - `T=2.5`: Moderate temperature value, balancing between random and ordered spin states.
  - `steps=10000`: Number of simulation steps for lattice evolution.
- After running, `plot_grid()` displays the final configuration of the lattice, showing domains of aligned spins.

### Summary
This code simulates the **Ising Model** using a Monte Carlo method (Metropolis algorithm) to visualize phase transitions in a 2D lattice. Through this, we observe how temperature influences magnetic alignment in materials, providing insights into critical phenomena and collective behavior in statistical physics.
