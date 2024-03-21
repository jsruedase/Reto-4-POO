# Reto-4-POO
Cuarto Reto

``` python
import math

class Point():
    def __init__(self, x, y):
        self._x = x
        self._y = y
        
    def distance(self, point):
        return math.sqrt((self._x - point.get_x())**2 + (self._y - point.get_y())**2)
    
    def set_x(self, x):
        self._x = x
    
    def set_y(self, y):
        self._y = y
    
    def get_x(self):    
        return self._x
    
    def get_y(self):    
        return self._y


class Line:
    def __init__(self, start, end) -> None:
        self._start = start
        self._end = end
        self._length = self.compute_length()
        self._slope = self.compute_slope()

    def compute_length(self):
        length = math.sqrt((self._start.get_x() - self._end.get_x())**2 + (self._start.get_y() - self._end.get_y())**2)
        return round(length,2)
    
    def compute_slope(self):
        try:
            m = (self._end.get_y() - self._start.get_y()) / (self._end.get_x() - self._start.get_x())
            return round(m,2)
        except:
            return float("inf")
        
    def compute_horizontal_cross(self):
        pos_x_coordinate = (self._slope * self._start.get_x() - self._start.get_y()) / self._slope

        if pos_x_coordinate >= self._start.get_x() and pos_x_coordinate <= self._end.get_x():
            return True
        else:
            return False
        
    def compute_vertical_cross(self):
        pos_y_coordinate = self._start.get_y() - (self._slope * self._start.get_x())

        if pos_y_coordinate >= self._start.get_y() and pos_y_coordinate <= self._end.get_y():
            return True
        else:
            return False
    
    def discretize_line(self, number_of_sections):
        array_of_points = []
        distance_points = self._length / number_of_sections
        
        if self._slope == float("inf"):
            for i in range(number_of_sections):
                array_of_points.append(Point(self._start.get_x(), self._start.get_y() + i * distance_points))
        elif self._slope == 0:
            for i in range(number_of_sections):
                array_of_points.append(Point(self._start.get_x() + i * distance_points, self._start.get_y()))
        else:
            for i in range(number_of_sections):
                array_of_points.append(Point(self._start.get_x() + i * distance_points, self._start.get_y() + self._slope * i * distance_points))
        
        for point in array_of_points:
            print(f"({point.get_x()}, {point.get_y()})")
    
    def get_start(self):
        return self._start

    def get_end(self):
        return self._end
    
    def get_length(self):
        return self._length
    
    def get_slope(self):
        return self._slope
    
    def set_start(self, start):
        self._start = start
        
    def set_end(self, end):
        self._end = end
        
    def set_length(self, length):
        self._length = length
        
    def set_slope(self, slope):
        self._slope = slope

class Shape:
    """
    Entra como parámetro una lista de vértices que forman la figura geométrica, en sentido antihorario. 
    """
    def __init__(self, vertices):
        self._vertices = vertices
        self._edges = self.compute_edges()
        self._is_regular = self.check_regular()
        
        
    def compute_edges(self):
        edges = []
        for i in range(len(self._vertices)):
            if i == len(self._vertices) - 1:
                edges.append(Line(self._vertices[i], self._vertices[0]))
            else:
                edges.append(Line(self._vertices[i], self._vertices[i+1]))
        return edges

    def compute_inner_angles(self):
        inner_angles = []
        for i in range(len(self._vertices)):
            if i == len(self._vertices) - 1:
                angle = math.degrees(math.acos((self._edges[i].get_length()**2 + self._edges[0].get_length()**2 - self._edges[i].get_length()**2) / (2 * self._edges[i].get_length() * self._edges[0].get_length())))
                inner_angles.append(round(angle,1))
            else:
                angle = math.degrees(math.acos((self._edges[i].get_length()**2 + self._edges[i+1].get_length()**2 - self._edges[i].get_length()**2) / (2 * self._edges[i].get_length() * self._edges[i+1].get_length())))
                inner_angles.append(round(angle,1))

        return inner_angles

    def compute_perimeter(self):
        perimeter = 0
        for edge in self._edges:
            perimeter += edge.get_length()
        return perimeter

    def compute_area(self):
        #Fórmula (de Gauss) para calcular el área de un polígono cualquiera
        area = 0
        for i in range(len(self._vertices)):
            if i == len(self._vertices) - 1:
                area += self._vertices[i].get_x() * self._vertices[0].get_y() - self._vertices[0].get_x() * self._vertices[i].get_y()
            else:
                area += self._vertices[i].get_x() * self._vertices[i+1].get_y() - self._vertices[i+1].get_x() * self._vertices[i].get_y()
        return abs(area) / 2

    def check_regular(self):
        if len(set(self.compute_inner_angles())) == 1 and len(set([edge.get_length() for edge in self._edges])) == 1:
            return True
        else:
            return False

class Rectangle(Shape):
    def __init__(self, vertices):
        super().__init__(vertices)
        self.width = self.get_width()
        self.height = self.get_height()
    
    def get_height(self):
        return self._edges[0].get_length()
    
    def get_width(self):
        return self._edges[1].get_length()  

    def compute_area(self):
        return self.width * self.height

    def compute_perimeter(self):
        return super().compute_perimeter()

class Square(Rectangle):
    def __init__(self, vertices):
        super().__init__(vertices)
        self.side = self.get_side()
    
    def get_side(self):
        return self.width

    def compute_area(self):
        return self.side**2

    def compute_perimeter(self):
        return super().compute_perimeter()

class Triangle(Shape):
    def __init__(self, vertices):
        super().__init__(vertices)
        self.base = self.get_base()
        self.height = self.get_height()
    
    def get_base(self):
        return self._edges[0].get_length()
    
    def get_height(self):
        return 2 * self.compute_area() / self.base

    def compute_area(self):
        return super().compute_area()

    def compute_perimeter(self):
        return super().compute_perimeter()

class Scalene(Triangle):
    def __init__(self, vertices):
        super().__init__(vertices)
        self.side1 = self.get_side1()
        self.side2 = self.get_side2()
        self.side3 = self.get_side3()
    
    def get_side1(self):
        return self._edges[0].get_length()
    
    def get_side2(self):
        return self._edges[1].get_length()
    
    def get_side3(self):
        return self._edges[2].get_length()

    def compute_area(self):
        s = self.compute_perimeter() / 2
        return math.sqrt(s * (s - self.side1) * (s - self.side2) * (s - self.side3))

    def compute_perimeter(self):
        return super().compute_perimeter()

class Isosceles(Triangle):
    def __init__(self, vertices):
        super().__init__(vertices)
        self.base = self.get_base()
        self.side = self.get_side()
    
    def get_base(self):
        return self._edges[0].get_length()
    
    def get_side(self):
        return self._edges[1].get_length()

    def compute_area(self):
        return (self.base * self.height) / 2

    def compute_perimeter(self):
        return super().compute_perimeter()

class Equilateral(Triangle):
    def __init__(self, vertices):
        super().__init__(vertices)
        self.side = self.get_side()
    
    def get_side(self):
        return self._edges[0].get_length()

    def compute_area(self):
        return (math.sqrt(3) / 4) * self.side**2

    def compute_perimeter(self):
        return super().compute_perimeter()

if __name__ == "__main__":
    forma = Shape([Point(0,0), Point(1,0), Point(1,1), Point(0,1)])
    for edge in forma.compute_edges():
        print(f"start = ({edge.get_start().get_x()}, {edge.get_start().get_y()}), end = ({edge.get_end().get_x()}, {edge.get_end().get_y()})")
    print(forma.compute_inner_angles())



"""
There are 10 items on the menu, which have their own prices. However, if certain items are ordered together, 
a discount can be applied to the combo. The combos are:
    Personal 1: CheeseBurger + Fries + Soda             -10%
    Personal 2: Pizza + Milkshake                       -20%
    Familiar: ChickenNuggets + HotDog + Steak + Salad   -30%

Aditionaly, the user can modify the size of some items, which will increase the base price. There are two options:
medium or large. The large size increases the base price by 25%.

Finally, there are three methods of payment: cash, card or nequi. The cash method has a 5% discount, the card increases
the total price by 5% and the nequi method has no discount or increase.
"""

class MenuItem:
    def __init__(self, name, price, method):
        self.name = name
        self.price = price
        self.method = method
    
        
class Burger(MenuItem):
    def __init__(self, name, price, type, with_vegetables, doneness, method) -> None:
        super().__init__(name, price, method)
        self._type = type
        self._with_vegetables = with_vegetables
        self._doneness = doneness
    
    def set_price(self, type):
        self.price = 15000
    
    def get_price(self):
        if self.type == "2":
            return 17000
        elif self.type == "3":
            return 18000
        else:
            return 15000
    
    
class Milkshake(MenuItem):
    def __init__(self, name, price, flavor, size, method) -> None:
        super().__init__(name, price, method)
        self.flavor = flavor
        self.size = size
    
    def set_price(self, size):
        self.price = 10000
        
    def get_price(self):
        if self.size == "2":
            return 12500
        else:
            return 10000

class Fries(MenuItem):
    def __init__(self, name, price, type, size, method) -> None:
        super().__init__(name, price, method)
        self.size = size
        self.type = type
    
    def set_price(self, type):
        self.price = 7000
    
    def get_price(self):
        if self.type == "2":
            return 8000
        else:
            return 7000
        
class Soda(MenuItem):
    def __init__(self, name, price, brand, size, method) -> None:
        super().__init__(name, price, method)
        self.brand = brand
        self.size = size
        
    def set_price(self, brand):
        self.price = 5000
    
    def get_price(self):
        return 5000
    
class Pizza(MenuItem):
    def __init__(self, name, price, size, type, method) -> None:
        super().__init__(name, price, method)
        self.size = size
        self.type = type
    
    def set_price(self, size):
        self.price = 10000
    
    def get_price(self):
        if self.size == "2":
            return 12500
        else:
            return 10000
        
class IceCream(MenuItem):
    def __init__(self, name, price, flavor, scoops, method) -> None:
        super().__init__(name, price, method)
        self.flavor = flavor
        self.scoops = scoops
        
    def set_price(self, scoops):
        self.price = 5000
    
    def get_price(self):
        if self.scoops == "2":
            return 7000
        else:
            return 5000
        
class HotDog(MenuItem):
    def __init__(self, name, price, sauces, method) -> None:
        super().__init__(name, price, method)
        self.sauces = sauces
    
    def set_price(self, sauces):
        self.price = 8000
    
    def get_price(self):
        return 8000

class ChickenNuggets(MenuItem):
    def __init__(self, name, price, pieces, method) -> None:
        super().__init__(name, price, method)
        self.pieces = pieces
    
    def set_price(self, pieces):
        self.price = 12000
    
    def get_price(self):
        if self.pieces == "2":
            return 22000
        else:
            return 12000

class Salad(MenuItem):
    def __init__(self, name, price, type, method) -> None:
        super().__init__(name, price, method)
        self.type = type
    
    def set_price(self, type):
        self.price = 5000
    
    def get_price(self):
        return 5000
        
class Steak(MenuItem):
    def __init__(self, name, price, doneness, method) -> None:
        super().__init__(name, price, method)
        self.doneness = doneness
    
    def set_price(self, doneness):
        self.price = 30000
    
    def get_price(self):
        return 30000
        
class Order():
    def __init__(self, items_list, payment) -> None:
        self.items_list = items_list
        self.payment = payment
        self.total = self.calculate_total()
        self.total_discounted = self.apply_discount(self.total)
        
    def calculate_total(self):  
        total = 0
        for item in self.items_list:
            try:                                    #Some items don't have size atribute
                if item.size == "2":
                    total += item.price * 1.25
                else:
                    total += item.price
            except:
                total += item.price
        return total

    def apply_discount(self, total):
        personal_combo = set(["CheeseBurger", "Fries", "Soda"])
        personal_combo2 = set(["Pizza", "Milkshake"])
        familiar_combo = set(["ChickenNuggets", "HotDog", "Steak", "Salad"])
        items = []
        for item in self.items_list:
            items.append(item.name)
        items = set(items)
        
        #Combos discounts
        if items == personal_combo:
            total = total * 0.9
            print("Personal combo discount applied")
        elif items == personal_combo2:
            total = total * 0.8
            print("Personal combo 2 discount applied")
        elif items == familiar_combo:
            total = total * 0.7
            print("Familiar combo discount applied")
        else:
            print("No combo discount applied")
        
        #Payments
        if self.payment == "cash":
            total = total * 0.95
            print("Cash payment applied")
        elif self.payment == "card":
            total = total * 1.05    
            print("Card payment applied")
        else:
            print("Nequi payment applied")
        return total

class Payment():
    def __init__(self, method) -> None:
        self.method = method
    def pay(self, amount):
        pass

class Cash(Payment):
    def __init__(self, method) -> None:
        super().__init__(method)
        
    def pay(self, total):
        amount = int(input("Please enter the amount of cash you have: "))
        if amount < total:
            print("Insufficient amount")
            return False
        else:
            print(f"You have paid {amount} COP successfully") 
            print(f"Your change is {amount - total} COP")
            return True

class Card(Payment):
    def __init__(self, method) -> None:
        super().__init__(method)
        
    def pay(self, total):
        card_number = input("Please enter the card number: ")
        pin = input("Please enter the pin: ")
        print(f"You have paid {total} COP successfully") 
        return True 

class Nequi(Payment):
    def __init__(self, method) -> None:
        super().__init__(method)
        
    def pay(self, total):
        phone_number = input("Please enter the phone number: ")
        pin = input("Please enter the pin: ")
        print(f"You have paid {total} COP successfully") 
        return True
    
if __name__ == "__main__":
    print("Welcome, please select the items you want to order:")
    print("1. CheeseBurger")
    print("2. Fries")
    print("3. Soda")
    print("4. Pizza")
    print("5. Milkshake")
    print("6. IceCream")
    print("7. HotDog")
    print("8. ChickenNuggets")
    print("9. Salad")
    print("10. Steak")
    print("0. Finish order")   
    payment = input("Please select the payment method: (cash, card, nequi)\n")
    ordering = True
    order_list = []
    
    while ordering:
        orden = input("Select the item you want to order or type 0 to finish the order: ")
        if orden == "1":
            type = input("Select the type of burger: \n1. Beef + 0$\n2. Chicken +2000$ \n3. Veggie +3000$\n")
            with_vegetables = input("Do you want it with vegetables? (yes/no)\n")
            doneness = input("How do you want it cooked? \n1. Rare\n2. Medium \n3. Well done \n")
            if type == "2":
                order_list.append(Burger("CheeseBurger", 17000, type, with_vegetables, doneness, payment))
            elif type == "3":
                order_list.append(Burger("CheeseBurger", 18000, type, with_vegetables, doneness, payment))
            else:
                order_list.append(Burger("CheeseBurger", 15000, type, with_vegetables, doneness, payment))
            
            
        elif orden == "2":
            type = input("Select the type of fries: \n1. Regular +0$\n2. Curly +1000$\n")
            size = input("Select the size: \n1. Medium +0$\n2. Large +1750$\n")
            if type == "2":
                order_list.append(Fries("Fries", 8000, type, size, payment))
            else:
                order_list.append(Fries("Fries", 7000, type, size, payment))
            
        elif orden == "3":
            brand = input("Select the brand of the soda: \n1. CocaCola \n2. Pepsi \n")
            size = input("Select the size: \n1. Medium +0$\n2. Large +1250$\n")
            order_list.append(Soda("Soda", 5000, brand, size, payment)) 
            
        elif orden == "4":
            size = input("Select the size of the pizza: \n1. Medium +0$\n2. Large +2500$\n")
            type = input("Select the type of pizza: \n1. Pepperoni +0$\n2. Hawaiian +0$\n")
            order_list.append(Pizza("Pizza", 10000, size, type, payment))
        
        elif orden == "5":
            flavor = input("Select the flavor of the milkshake: \n1. Vanilla \n2. Chocolate \n")
            size = input("Select the size: \n1. Medium +0$\n2. Large +2500$\n")
            order_list.append(Milkshake("Milkshake", 10000, flavor, size, payment))
            
        elif orden == "6":
            flavor = input("Select the flavor of the ice cream: \n1. Vanilla +0$\n2. Chocolate +0$\n")
            scoops = input("Select the number of scoops: \n1. One +0$\n2. Two +2000$\n")
            if scoops == "2":
                order_list.append(IceCream("IceCream", 7000, flavor, scoops, payment))
            else:
                order_list.append(IceCream("IceCream", 5000, flavor, scoops, payment))
            
        
        elif orden == "7":
            sauces = input("Select the sauces: \n1. Ketchup \n2. Mustard \n")
            order_list.append(HotDog("HotDog", 8000, sauces, payment))
        
        elif orden == "8":
            pieces = input("Select the number of pieces: \n1. Six \n2. Twelve +10000$\n")
            if pieces == "2":
                order_list.append(ChickenNuggets("ChickenNuggets", 22000, pieces, payment))
            else:
                order_list.append(ChickenNuggets("ChickenNuggets", 12000, pieces, payment))

        elif orden == "9":
            type = input("Select the type of salad: \n1. Caesar \n2. Garden \n")
            order_list.append(Salad("Salad", 5000, type, payment))
        
        elif orden == "10":
            doneness = input("How do you want the steak cooked? \n1. Rare \n2. Medium \n3. Well done \n")
            order_list.append(Steak("Steak", 30000, doneness, payment))
            
        elif orden == "0":
            print("Order finished")
            ordering = False
        else:
            print("Invalid, please select a correct option")
            
    order = Order(order_list, payment)
    print(f"Total price: {order.calculate_total()}")
    print(f"Total price according to payment method and discount combos: {order.apply_discount(order.calculate_total())}")
    
    if payment == "cash":
        cash = Cash(payment)
        cash.pay(order.total_discounted)          
    elif payment == "card": 
        card = Card(payment)
        card.pay(order.total_discounted)
    else:        
        nequi = Nequi(payment)
        nequi.pay(order.total_discounted)
``` 
