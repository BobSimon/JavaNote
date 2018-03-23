###基本定义
为创建一组相关或相互依赖德对象提供一个接口，而且无需指定他们的具体类。
抽象工厂模式允许客户端使用抽象的接口来创建一组相关的产品，而不需要关心实际产出的具体产品是什么，这样客户就可以从具体的产品中被解耦。
###类图结构
![](https://i.imgur.com/bUb8q02.png)
说明：
- AbstractFactory：抽象工厂，抽象工厂定义了一个接口，所有的具体工厂都必须实现此接口，这个接口包含了一组方法用来生产产品。
- CreateFactroy：具体工厂，具体工厂是用来生产不同的产品，要创建一个产品，客户只需要使用其中一个工厂，完全不需要实例化任何产品对象。
- AbstractProduct:抽象产品，每一个具体工厂都能够生产一整组产品。
- Product:具体的产品。

###模式实现
给不同的披萨分店生产原料，不同的分店使用的不同的一组原料。例如
![](https://i.imgur.com/uOdx9zf.png)
###创建抽象工厂PizzaIngredientFactory

	/**
	 * 原料抽象工厂，负责创建所有的原料
	 *@author lky
	 *@date 2018年1月17日
	 */
	public interface PizzaIngredientFactory {
		public Dough createDough();// 面团
		
		public Sauce createSauce();// 酱汁
		
		public Cheese createCheese();// 奶酪
		
		public Veggies[] createVeggies();// 蔬菜
		
		public Pepperoni createPepperoni();// 意式腊肠
		
		public Clams createClams();// 肉
	
	}
抽象原料工厂创建后，为每一个区域造一个分厂，创建一个继承PizzaIngredientFactory的子类来实现每一个创建方法。
###纽约原料工厂NYPizzaIngredientFactory

