# Exercise 2: Create your first ML model

## Example 1: Predicting Housing Prices
`simple_ml_example.py`:

```python
import numpy as np
from sklearn.linear_model import LinearRegression

# Small dataset: house_size -> price
sizes = np.array([[1000], [1500], [2000], [2500], [3000]])
prices = np.array([200000, 300000, 400000, 500000, 600000])

# Train model
model = LinearRegression()
model.fit(sizes, prices)

# Make predictions
new_size = np.array([[2200]])
predicted_price = model.predict(new_size)
print(f"House size: {new_size[0][0]} sq ft")
print(f"Predicted price: ${predicted_price[0]:,.2f}")
```
