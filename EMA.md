#### **EMA (Exponential Moving Average)**

- **Formula**:  
    ![[Screenshot 2025-11-21 at 8.45.52 PM.png]]
    - **Reasoning**: The EMA gives more weight to recent prices, making it more responsive to price changes than a simple moving average (SMA). This makes it popular in trading because it reacts faster to recent price movements.
        
    - **IIR Filter**: EMA is also known as an Infinite Impulse Response (IIR) filter, which means it uses both the current input (price) and all previous values to calculate the result. The influence of older values decays exponentially, hence the name "exponential" moving average.
        
    - **Implementation**:
        
        - **Efficient single-pass recursion**: This means we can calculate the EMA in one pass over the data without storing all previous values.
            
        - **Vectorized via `pandas.Series.ewm`**: This function allows you to efficiently compute EMA on an entire time series of prices using pandas. The "vectorized" approach avoids slow Python loops by leveraging optimized C-based implementations.