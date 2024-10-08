# 3D Visualization of Electric Potential Distribution Between Two Capacitor Plates

This project simulates the electric potential distribution between two capacitor plates and visualizes the results using a 3D plot. The top plate is assigned a fixed voltage while the bottom plate is grounded. The voltage distribution between the plates is computed using the **Gauss-Seidel** iterative method.

---

## Table of Contents
- [Introduction](#introduction)
- [Features](#features)
- [Installation](#installation)
- [Usage](#usage)
- [Code](#code)
- [Visualization](#visualization)
- [Parameters](#parameters)
- [Theory](#theory)
- [Future Improvements](#future-improvements)


---

## Introduction

Capacitors are fundamental components in electrical circuits, storing energy in the form of an electric field. In this project, we visualize the electric potential between two parallel capacitor plates. Using a computational grid, we solve for the potential at each point between the plates, demonstrating the relationship between voltage and electric field.

The main goal of this project is to create a simulation that helps in understanding the electric potential distribution in a 2D grid placed between two parallel plates and visualize this distribution in 3D.

---

## Features

- Simulation of electric potential distribution using boundary conditions.
- Gauss-Seidel iterative method for solving the potential at inner grid points.
- 3D visualization of the potential between the plates.
- Adjustable grid size and boundary voltage.
- Top and bottom capacitor plates displayed on the 3D plot.

---

## Installation

To run this project, follow these steps:

1. Clone the repository:

    ```bash
    git clone https://github.com/UKD1211/FDM_based_Node_Potential_Calculation_for_Capacitor_Plates
    cd FDM_based_Node_Potential_Calculation_for_Capacitor_Plates
    ```

2. Install the required Python libraries:

    ```bash
    pip install numpy matplotlib
    ```

---

## Usage

Once you have installed the necessary libraries, you can run the Python script:

```bash
python capacitor_visualization.py
```

## Code

```bash
import numpy as np
import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D

def calculate_inner_node_voltages(N, top_voltage):
    """
    Calculate the voltages at the inner nodes based on boundary conditions.
    Args:
        N: Number of grid points per side, including boundary.
        top_voltage: Voltage at the top boundary.
    Returns:
        voltages: List of voltages at the inner nodes.
    """
    # Calculate electric field E
    E = top_voltage / (N-1)
    
    # Define the boundary values
    boundary_top = [top_voltage] * N
    boundary_bottom = [0] * N
    boundary_left = [top_voltage - i * E for i in range(1,N-1)]
    boundary_right = [top_voltage - i * E for i in range(1,N-1)]
    
    # Initialize the voltages at all the nodes
    voltages = np.zeros((N, N))
    
    # Set boundary conditions
    voltages[0, :] = boundary_top
    voltages[-1, :] = boundary_bottom
    voltages[1:-1, 0] = boundary_left
    voltages[1:-1, -1] = boundary_right
    
    # Gauss-Seidel iteration to solve for the inner node voltages
    max_iterations = 10000
    tolerance = 1e-6
    
    for iteration in range(max_iterations):
        old_voltages = np.copy(voltages)
        
        # Update the voltages for each inner node
        for i in range(1, N-1):
            for j in range(1, N-1):
                voltages[i, j] = 0.25 * (voltages[i+1, j] + voltages[i-1, j] + voltages[i, j+1] + voltages[i, j-1])
        
        # Check for convergence
        diff = np.linalg.norm(voltages - old_voltages)
        if diff < tolerance:
            print(f"Converged after {iteration + 1} iterations.")
            break
    else:
        print("Did not converge within the maximum number of iterations.")
    
    # Return voltages
    return voltages

# Parameters
top_voltage = float(input("Enter the top boundary voltage (e.g., 500V): "))  # Top boundary voltage
N = (int(input("Enter the number of grid points per side, including boundary (e.g., 5): "))+1)   # Number of grid points

# Calculate inner node voltages
voltages = calculate_inner_node_voltages(N, top_voltage)

# Print the final inner node voltages
print("\nFinal node voltages:")
print(voltages)

# 3D Plot: Showing Two Plates with the Grid in Between
fig = plt.figure()
ax = fig.add_subplot(111, projection='3d')

# Create grid of x, y coordinates
x = np.linspace(0, N-1, N)
y = np.linspace(0, N-1, N)
X, Y = np.meshgrid(x, y)

# Plot the surface for the voltage distribution
surf = ax.plot_surface(X, Y, voltages, cmap='viridis', edgecolor='k')

# Add the capacitor plates
# Top plate at z = top_voltage (high voltage)
ax.plot_surface(X, Y, top_voltage * np.ones_like(voltages), color='red', alpha=0.6, label='Top Plate')

# Bottom plate at z = 0 (grounded)
ax.plot_surface(X, Y, np.zeros_like(voltages), color='blue', alpha=0.6, label='Bottom Plate')

# Labels and title
ax.set_title(f"3D Voltage Distribution Between Two Capacitor Plates ({int(top_voltage)}V Top, Grounded Bottom)")
ax.set_xlabel("X Position")
ax.set_ylabel("Y Position")
ax.set_zlabel("Voltage (V)")

# Color bar for the voltage distribution
fig.colorbar(surf, ax=ax, shrink=0.5, aspect=5)

# Show the plot
plt.show()
```
## Visualization
After running the Python script, a 3D plot is generated displaying the voltage distribution between the two capacitor plates:

* Red Plate: The top plate at the specified voltage (e.g., 500V).
* Blue Plate: The grounded bottom plate (0V).
* Surface Plot: Represents the electric potential at each grid point.


## Parameters
* Top Boundary Voltage: The voltage applied to the top plate (default: 500V).
* Grid Points (N): The number of grid points (including boundary points) in both directions. This controls the resolution of the potential field between the plates.
* Iterations: The Gauss-Seidel method runs until convergence or the maximum number of iterations is reached.

## Theory
Capacitors consist of two conductive plates separated by a dielectric medium. When voltage is applied to one plate (top), an electric field is established between the plates, and this field stores energy. The potential difference between the plates is calculated using boundary conditions, where one plate is held at a high potential (top boundary) and the other is grounded (bottom boundary).

The electric potential at any point between the plates is influenced by the boundary conditions and the distance from the plates. The Gauss-Seidel iterative method is used to numerically compute the potential at each point between the plates.

## Future Improvements
* Implementing different boundary conditions (e.g., varying voltages on the sides).
* Adding a time-dependent electric field to simulate dynamic behavior.
* Supporting non-parallel plates and other geometries.
* Optimizing the Gauss-Seidel solver for faster convergence.


