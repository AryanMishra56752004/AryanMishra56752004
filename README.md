from math import radians, sin, cos, acos, sqrt
import re
import cmath

'''
 We have to maintain a constant height of UAV at 20M above ground level
 UAV will have an initial velocity of 0m/s in X direction & 5 m/s
 in Z direction after 4 seconds UAV will have a constant velocity
 of 5 m/s in X direction.
 UAV is carrying a payload of 1.5KG
 Coordinates of TakeOFF Location Latitude--50d 03m 59s N, Longitude--005d 42m 53sW
 Coordinates of Target Location Latitude--58d 38m 38sN, Longitude--003d 04m 12s W
 '''

def distance(R, slat, slong, elat, elong):
    d=R*acos(sin(radians(slat))*sin(radians(elat))+
             cos(radians(slat))*cos(radians(elat))*cos(radians(slong-elong)))
    return d

def maneuver(vx,vz,u,fd,d):
    vz=18
    print("Ascending till distance from ground is 20m (at a rate of 18kmph along z)\n")
    #print("After 4 sec velocity set to 18kmph")
    vz=0
    print("After 4 seconds velocity along z axis set to 0 kmph\n")
    vx=vx+u 
    print("Horizontal velocity set to 18kmph\n")
    t=sqrt(2*20/10)/3600 # Time taken for payload to reach ground after release (in hr)
    ax=fd/1.5
    s=float(((vx*t)-0.5*ax*t*t))
    print("Payload has to be dropped",s,"Kms ahead of the Target Location At: \n")
    relcoor(d-s)
    c=str(input(print("Press R to release Payload or A to abort the mission!! ")))
    if(c=="R" or c=="r"):
        print("Payload Succesfully Released!!")
    elif(c=="A" or c=="a"):
        print("Mission Aborted!!")
    return 0

def dms2dd(degrees, minutes, seconds, direction):
    dd = float(degrees) + float(minutes)/60 + float(seconds)/(60*60)
    if direction == 'E' or direction == 'S':
       dd *= -1
    return dd

def dd2dms(deg):
    d = int(deg)
    md = abs(deg - d) * 60
    m = int(md)
    sd = (md - m) * 60
    return [d, m, sd]

def parse_dms(dms):
    parts = re.split('[^\d\w]+', dms)
    lat = dms2dd(parts[0], parts[1], parts[2], parts[3])
    return lat

# Coordinates of Release Location
def rectopol(slat, slong, elat, elong):
    inpnum=complex((elat-slat),(elong-slong))
    phi,r=cmath.polar(inpnum)
    print("Bearing... ",phi,"°")
    #dd2dms(phi)
    return phi,r

def relcoor(d): 
    phi,r= rectopol(slat, slong, elat, elong)
    deltaN= float(d*sin(radians(phi)))
    deltaW=float(d*cos(radians(phi)))
    #deltaN = cmath.rect(d,phi)[0]
    #deltaW= cmath.rect(d,phi)[1]
    N= deltaN+(slat)
    W=deltaW+(slong) 
    print(dd2dms(N)[0],"°",dd2dms(N)[1],"'",dd2dms(N)[2],"\" N")
    print(dd2dms(W)[0],"°",dd2dms(W)[1],"'",dd2dms(W)[2],"\" W")
    
slat = parse_dms("50°3'59 N")
slong=parse_dms("5°42'53 W")
elat=parse_dms("58°38'38 W")
elong=parse_dms("3°4'12 W")

R= 6371.0 # in kilometres
s=str(input("Press Y to TakeOFF:  "))
if(s=="Y" or s=="y"):
    print("UAV is ready to TakeOFF Now!!")
    print("\n")

print("TakeOFF Location--> 50.066389 N 5.714722 W")
print("Target Location-->  58.643889 N 3.07 W")
print("\n")
print("Mission --> Drop a Payload of 1.5 KG at the Target Location....")
print("Distance between Target Location & Take OFF location...: ",distance(R, slat, slong, elat, elong))
# velocity of air(along the flow of UAV)(in kmph)
u=14.4
# velocity of UAV in x dir (in kmph)
vx=0
#velocity of UAV in y dir (in kmph)
#vy=0
#velocity of UAV in z direction
vz=0
# dimension of payload
r=0.1
A = 3.14*r*r  # area of reference (in m^2)(spherical body)
cd=0.47 # assuming an air drag coefficient of 0.47
fd=cd*A/((vx-u)*(vx-u)*2)
maneuver(vx,vz,u,fd,distance(R, slat, slong, elat, elong))


