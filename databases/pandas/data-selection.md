Boolean indexing
df[condition]

loc
iloc

Sampling
replace=False
replace=True

Examples:

df[df["price"] < 300000]

df.iloc[10:15, 2:5]

df.sample(5, replace=False)
