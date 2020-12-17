# EditReadMe

## Software and libraries

Software: Python
Libraries: Scipy, math and numpy

### MAIN SCRIPT

import numpy as np
from scipy.optimize import minimize
import math

#OBJECTIVE
price_list = [] #empty list created for each calculated cost from the code 

#VARIABLES
r_list = [] #empty list for outer radius
w_list = [] #empty list for thickness
h_list = [] #empty list for nacelle height
materials_list = ['Iron', 'Steel', 'Aluminium', 'S-glass epoxy', 'Carbon epoxy'] #names of the materials iterated through

#density (kg/m3)
set1 = [7150,7700,2700,1800,1600]

#price (£ per kg, assumed from general research)
set2 = [0.1,0.75,0.60,20,26]

#Youngs modulus 
set3 = [100000000000,205000000000,73000000000,39500000000,142000000000]

#iterating through each material and their properties
for i in range (5):
    A = set1[i]
    B = set2[i]
    C = set3[i]
    

    #objective function (find the volume of the optimised geometry and then times it by the density and cost per kg of each material)
    def objective(x):
        return (2*math.pi*x[1]*x[0]*x[2]+math.pi*x[1]*x[1]*x[2]+2*x[1]*math.pi*x[0]*x[0]-4*math.pi*x[0]*x[1]*x[1]+2*math.pi*x[1]*x[1]*x[1])*A*B
        
    #the nacelle must be strong enough to carry 2 men weighing 100 kg each with feet with bottom surface area of 300 cm2 (safety factor of 8 due to high levels of uncertainties)
    def constraint1(x):
        return ((C*x[1]*(x[0]*x[0]-2*x[0]*x[1]+x[1]*x[1]+x[0]*x[0]))/x[2]) - 130800
    
    #the inner radius must be larger than 1.6m 
    def constraint2(x):
        return x[0]-x[1]-1.6

    #the height must be at least 6 times longer than the outer radius (assumption) 
    def constraint3(x):
        return x[2]-6*x[0]

    #the outer radius must be larger than the top diameter of the tower (1.7m) (for good weight distribution)
    def constraint4(x):
        return x[0]-1.7



    # initial guesses
    n = 4
    x0 = np.zeros(n)
    x0[0] = 10
    x0[1] = 5.0
    x0[2] = 5.0

    # optimize
    b = (0.01,200000000.0)
    bnds = (b, b, b, b)
    con1 = {'type': 'ineq', 'fun': constraint1}
    con2 = {'type': 'ineq', 'fun': constraint2} 
    con3 = {'type': 'ineq', 'fun': constraint3} 
    con4 = {'type': 'ineq', 'fun': constraint4} 

    cons = ([con1,con2,con3,con4])
    solution = minimize(objective,x0,method='SLSQP',\
                        bounds=bnds,constraints=cons)
    x = solution.x

    # add solutions found to their relevant lists
    
    r_list.append(str(x[0]) + ' m')
    w_list.append(str(x[1]) + ' m')
    h_list.append(str(x[2]) + ' m')
    
    price_list.append(str(objective(x)))
    
#identifying the index of the optimum result which is the minimum number added to the price list
place = price_list.index(min(price_list))

#printing out each optimum variable
print ('Optimum material: ' + str(materials_list[place]))
print ('Optimum radius: ' + str(r_list[place]))
print ('Optimum thickness: ' + str(w_list[place]))
print ('Optimum height: ' + str(h_list[place]))

#printing out the final objective
print ('Final Objective: ' + '£' + str(price_list[place]))
    
Optimum material: Aluminium
Optimum radius: 1.6999999678455564 m
Optimum thickness: 0.07297645134766179 m
Optimum height: 10.19999999184896 m
Final Objective: £15123.150727854616

#### Performance

Execution time: <5sec
Processsor: intel i5
Operating system: MacOS



In [ ]:
 
