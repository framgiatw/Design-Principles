/*
 * To change this license header, choose License Headers in Project Properties.
 * To change this template file, choose Tools | Templates
 * and open the template in the editor.
 */
```
package homework;

/**
 *
 * @author Hoangduy
 */
interface AnimalInterface {      //Interface Segregation Principle Violation
    public boolean flyable();
    public boolean eatable();
}

class Dog implements AnimalInterface {
    @Override
    public boolean flyable() {
        throw new UnsupportedOperationException("Not supported yet."); //To change body of generated methods, choose Tools | Templates.
    }

    @Override
    public boolean eatable() {
        return true;
    }
}

class Bird implements AnimalInterface {
 
    @Override
    public boolean flyable() {
        return true;
    }

    @Override
    public boolean eatable() {
        return true;
    }    
}

class Feedable {//Single Responsibility Principle Violation & Open-Closed Principle
    private float weight;
    
    public float getWeight() {
        return weight;
    }
    
    public void setWeight(float weight) {
        this.weight = weight;
    }
    
    public void flyable(Animal charecter){
        if(charecter.fly()){
            System.out.printf("it can Fly");
        }
    }
    
    public void weightFood(Animal charecter) {
        setWeight(50);
        if(charecter.eat()) {
            if(getWeight() < 30) {
                System.out.printf("foof/day = 2kg");
            }
            else 
                System.out.printf("foof/day = 5kg");
        }
        System.out.printf("no food");
    }
    
}

// Refactored

interface flyable {
    public boolean fly();
}

interface eatable {
    public boolean eat();
}

class Dog implements eatable {

    @Override
    public boolean eat() {
        return true;
    }
}

class Bird implements flyable, eatable {

    @Override
    public boolean fly() {
        return true;
    }

    @Override
    public boolean eat() {
        return true;
    }
}

class checkFood {
    private float weight;
    
    public float getWeight() {
        return weight;
    }
    
    public void setWeight(float weight) {
        this.weight = weight;
    }
    public void weightFood(Animal charecter) {
        setWeight(50);
        if(charecter.eat()) {
            if(getWeight() < 30) {
                System.out.printf("foof/day = 2kg");
            } else 
                System.out.printf("foof/day = 5kg");
        }
        System.out.printf("no food");
    }
}

class checkFly {
    public void checkFly(Animal charecter){
        if(charecter.fly()){
            System.out.printf("it can Fly");
        }
    }
}
```