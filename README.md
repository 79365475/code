# code
!APDL code for M.heat source.
CMSEL,ALL
*GET,EMAX,ELEM„NUM,MAX
*GET,EMIN,ELEM„NUM,MIN
ALLSEL
TIME_WELD=5.9
Time_inc=0.2
CSI2=0.88
CSI3=24
CSI4=220
Ro=5
Yi=10
Vel=6.67
YY=0.5
τ=0
Pi=3.14
NPT=TIME_WELD/Time_inc
NROPT, FULL
*DO,i,1,NPT,1
WTIME=(i*Time_inc)
TIME,WTIME
*DO,jj,EMIN,EMAX,1
82X=CENTRX(jj)
Y=CENTRY(jj)
Z=CENTRZ(jj)
CSI=Z+Vel*(τ-WTIME)
RT=sqrt(X**2+CSI**2)
*IF,RT,GT,Ro,THEN
Q=0
*ELSEIF,Y,GE,YY,THEN
Qo=(3*CSI2*CSI3*CSI4)/Pi*(Ro**2)
Qv=exp((-3)*(RT**2)/(Yi)**2)
Q=Qo*Qv
*ELSE
Q=0
*ENDIF
BFE,jj,HGEN„Q
*ENDDO
NSUB, 1, 1, 1
SOLVE
*ENDDO
CMSEL,ALL
BFEDELE,ALL,ALL
ALLSEL
Cooling_time=1000
TIME,Cooling_time
NSUBST,1000,1000,1000
SOLVE


#code for martensite phase
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
# Define the value of Am
Am = 0.00022
Tm=642.51 # Load the Excel file into a DataFrame (the data is in "ngenzi.xlsx"
in the second column)
data = pd.read_excel("ngenzi.xlsx", usecols=[1], header=None)
# Convert the column to a numeric type, coercing non-convertible values to NaN
data.iloc[:, 0] = pd.to_numeric(data.iloc[:, 0], errors=’coerce’)
# Filter the data to select T values within the desired range (642.51 to 450)
filtered_data = data[(data.iloc[:, 0] >= 450) & (data.iloc[:, 0] <= 642.51)]
# Extract the T values from the filtered data
T_values = filtered_data.iloc[:, 0]
# Calculate the result using the equation for each T value
results = 1 - np.exp(-Am * (Tm - T_values))
# Create a plot of the results
plt.plot(T_values, results)
plt.xlabel("T")
plt.ylabel("Result")
plt.title("martensite volume fraction")
#plt.grid(True)
plt.show()
