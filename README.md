# Automated Loan Estimation System

## DESCRIPTION
I have changed the topic of my project in very subtle manner. In the previous Rule based Jess System. We have to determine a result based on parameters entered by the user or based on logic in code. But when it comes to Fuzzy values not all values can be converted to a Fuzzy variable, for example, whether a person is Male or Female, we cannot possibly fuzzify the gender of a person. There are also factors in a real world situation which can be fuzzyfied, like the height of a person, the income of person. To bring out the concept of Fuzzy variables, let us change the topic of the Project from Automated Loan Approval System to Automated Loan Estimation System.

Loan estimations are always necessary either for a banker or a customer. The amount of loan an individual can borrow can depends several metrics or factors, banks and organizations need to know exactly how much the customer can borrow before lending the person any money. The banker would need to check whether the customer can pay back the loan based on several factors of the customer such Income, Marital Status, etc. Therefore, it would be useful when the banker would need to check up to what amount the customer can borrow. And it would help the customer in checking how much he/she can borrow from the bank and plan future expenses. To do this credit analysis certain banks or organizations may take days or weeks to get back customer with information on how much one can borrow. An automated system to estimate how much a person can borrow is highly useful in this regard. Basic information from the customer has to be collected to make an estimate. So in this program, the different metrics that are collected from the user are Age, Gender, Job, Education, Credit Score,  Marital Status, Salary and Other Income out of which Age, Credit Score, Salary and Other Income are Fuzzy Variables. The Fuzzy Jess program then determines the estimation of how much loan can be borrowed by the customer by defuzzifying the Fuzzy Variable, Loan which has a range of $0-$200,000. The various categories for which a loan can be estimated are  given in comments in the Jess Program (LoanEstimation_pat.clp).

A flowchart for the Fuzzy Jess Automated Loan Estimation System is given below:-


## KNOWLEDGE BASE
### KNOWLEDGE BASE DESCRIPTION
The Knowledge Based here is given by the user to the Program to make decisions based the on the facts that the user enters. The facts in the program are: -
* ?*age* :- The age of the customer (Fuzzy Variable)
* customer-gender :- The gender of the customer
* customer-edu :- The education level of the customer
* ?*cred* :- The approximate credit score of the customer (Fuzzy Variable)
* customer-mar :- The marital status of the customer
* customer-job :- whether the customer has a job or not
* ?*salary* :- Salary of the customer if the customer has a job (Fuzzy Variable)
* ?*oin* :- Other sources of income of the customer is the customer has no job (Fuzzy Variable)
* ?*loan* :- The range of loan amount that is going to be estimated for the user (Fuzzy Variable)
2
### KNOWLEDGE BASE IN JESS

**NOTE: -** The Knowledge base is given below in Jess. The knowledge base is determined by the user, as the user enters information, the choices are asserted, hence facts are defined and this is used as KB to make decisions.

```
; RULE 1
(defrule initial-terms
    (declare (salience 100))
=>
(import nrc.fuzzy.*)

(load-package nrc.fuzzy.jess.FuzzyFunctions)

;;;;;;;;;;;;;;;;;;;;;
; Primary Terms
;;;;;;;;;;;;;;;;;;;;;;;

(?*age* addTerm "low" (new ZFuzzySet 0.00 40.00))
(?*age* addTerm "medium" (new nrc.fuzzy.TriangleFuzzySet 38.00 44.00 55.00))
(?*age* addTerm "high" (new SFuzzySet 50.00 80.00))

(?*cred* addTerm "low" (new ZFuzzySet 300.00 500.00))
(?*cred* addTerm "medium" (new nrc.fuzzy.TriangleFuzzySet 400.00 600.00 800.00))
(?*cred* addTerm "high" (new SFuzzySet 750.00 900.00))

(?*salary* addTerm "low" (new ZFuzzySet 0.0 50000.00))
(?*salary* addTerm "medium" (new nrc.fuzzy.TriangleFuzzySet 40000.00 70000.00 95000.00))
(?*salary* addTerm "high" (new SFuzzySet 90000.0 150000.0))
    
(?*oin* addTerm "low" (new ZFuzzySet 0.0 3000.00))
(?*oin* addTerm "medium" (new nrc.fuzzy.TriangleFuzzySet 2000.00 4500.00 7000.00))
(?*oin* addTerm "high" (new SFuzzySet 6000.0 10000.0))
    
(?*loan* addTerm "cat1" (new ZFuzzySet 0.0 15000.0))
(?*loan* addTerm "cat2" (new PIFuzzySet 27000.0 10000.0))
(?*loan* addTerm "cat3" (new PIFuzzySet 55000.0 25000.0))
(?*loan* addTerm "cat4x" (new PIFuzzySet 60000.0 54000.0))
(?*loan* addTerm "cat4" (new PIFuzzySet 77000.0 58000.0))
(?*loan* addTerm "cat5x" (new PIFuzzySet 105000.0 75000.0))
(?*loan* addTerm "cat5" (new SFuzzySet 100000.0 200000.0))

)

;;;;;;;;;;;;;;;;;;;;;
; DEFFUNCTIONS
;;;;;;;;;;;;;;;;;;;;;;;

; Ask the user Age and Gender

(deffunction print-age-gender()
    (printout t crlf)
    (printout t "----------|WELCOME TO AUTOMATED LOAN ESTIMATION SYSTEM|----------" crlf)
    (printout t "Kindly answer the following questions to start:-" crlf crlf)
    (printout t "Enter your age(ex. 34)" crlf)
    (printout t "Please enter a value between (1-98): ")
    (bind ?age-value (float (readline t)))

    (printout t crlf)
    (printout t "Are you a male/female?" crlf)
    (printout t "1.Male" crlf)
    (printout t "2.Female" crlf)
    (printout t "Enter your choice(1/2):-" crlf crlf)
    (bind ?gender (read))


    (assert(theAge
        (new nrc.fuzzy.FuzzyValue ?*age*
        (new nrc.fuzzy.TriangleFuzzySet
        (- ?age-value 1.0)
        ?age-value
        (+ ?age-value 1.0)))))

	(assert (customer-gender (gender ?gender)))
    )

; Ask user the Level of Education and Credit Score

(deffunction ask-edu-credit()
    (printout t crlf)
    (printout t "What is your highest level of education?" crlf)
    (printout t "1. High School" crlf)
    (printout t "2. Bachelors" crlf)
    (printout t "3. Masters" crlf)
    (printout t "Enter your choice(1/2/3):-" crlf crlf)
    (bind ?edu (read))
    
    (printout t crlf)
    (printout t "What does your credit score looking like?" crlf)
    (printout t "Enter your choice(eg. 740):-" crlf)
    (printout t "Please enter a value between (305-895): ")
    (bind ?credenter (float (readline t)))
    
    (assert (customer-edu (edu ?edu)))
	;(assert (customer-cred (cred ?cred)))
    (assert(theCred
    (new nrc.fuzzy.FuzzyValue ?*cred*
    (new nrc.fuzzy.TriangleFuzzySet
    (- ?credenter 5.0)
    ?credenter
    (+ ?credenter 5.0)))))
    )

; Ask user the Marital Status

(deffunction ask-marital()
    (printout t crlf)
    (printout t "What is your marital status?" crlf)
    (printout t "1. Single" crlf)
    (printout t "2. Married" crlf)
    (printout t "3. Divorced" crlf)
    (printout t "4. Seperated" crlf)
    (printout t "Enter your choice(1/2/3/4):-" crlf crlf)
    (bind ?mar (read))
    
    (assert (customer-mar (mar ?mar)))
    )

; Ask user whether the user has a job or not

(deffunction ask-job()
    (printout t crlf)
	(printout t "Do you have a Job?" crlf)
    (printout t "1.Yes" crlf)
    (printout t "2.No" crlf)
    (printout t "Enter your choice(1/2):-" crlf crlf)
    (bind ?job (read))
    
    (assert (customer-job (job ?job)))
)

; Ask the user the range of Salary he/she recieves

(deffunction ask-sal()
    (printout t crlf)
    (printout t "What is your Salary? (eg. 25000)" crlf)
    (printout t "Please enter a value between ($100-$149900): ")
    (bind ?sal (float (readline t)))
    
    (assert(theSalary
    (new nrc.fuzzy.FuzzyValue ?*salary*
    (new nrc.fuzzy.TriangleFuzzySet
    (- ?sal 100.0)
    ?sal
    (+ ?sal 100.0)))))
    
    )

; Ask user whether the user has any other sources of income

(deffunction ask-oin()
    (printout t crlf)
    (printout t "Do you have any other sources of income?" crlf)
    (printout t "Enter amount(eg. 7500):-" crlf)
    (printout t "Please enter a value between ($5-$9995): ")
    (bind ?oine (float (readline t)))
    
    (assert(theOin
    (new nrc.fuzzy.FuzzyValue ?*oin*
    (new nrc.fuzzy.TriangleFuzzySet
    (- ?oine 1.0)
    ?oine
    (+ ?oine 1.0)))))
    )
```
## TEST CASES
### TEST CASE 1
```
----------|WELCOME TO AUTOMATED LOAN ESTIMATION SYSTEM|----------
Kindly answer the following questions to start:-

Enter your age(ex. 34)
Please enter a value between (1-98): 56

Are you a male/female?
1.Male
2.Female
Enter your choice(1/2):-

1

What is your highest level of education?
1. High School
2. Bachelors
3. Masters
Enter your choice(1/2/3):-

3

What does your credit score looking like?
Enter your choice(eg. 740):-
Please enter a value between (305-895): 875

What is your marital status?
1. Single
2. Married
3. Divorced
4. Seperated
Enter your choice(1/2/3/4):-

2

Do you have a Job?
1.Yes
2.No
Enter your choice(1/2):-

1

What is your Salary? (eg. 25000)
Please enter a value between ($100-$149900): 123000

THANK YOU FOR YOUR PATIENCE! HERE IS YOUR ESTIMATION:-
Good day, we would like to inform that you come under CATEGORY 3.
This category states that you are an individual with a Job, with a High
or Medium salary, with a High or Good Credit Score and with a Mid/Elderly age.

The estimated Loan that you can borrow:- $77000
```
### TEST CASE 2
```
----------|WELCOME TO AUTOMATED LOAN ESTIMATION SYSTEM|----------
Kindly answer the following questions to start:-

Enter your age(ex. 34)
Please enter a value between (1-98): 34

Are you a male/female?
1.Male
2.Female
Enter your choice(1/2):-

1

What is your highest level of education?
1. High School
2. Bachelors
3. Masters
Enter your choice(1/2/3):-

2

What does your credit score looking like?
Enter your choice(eg. 740):-
Please enter a value between (305-895): 568

What is your marital status?
1. Single
2. Married
3. Divorced
4. Seperated
Enter your choice(1/2/3/4):-

1

Do you have a Job?
1.Yes
2.No
Enter your choice(1/2):-

1

What is your Salary? (eg. 25000)
Please enter a value between ($100-$149900): 67000

THANK YOU FOR YOUR PATIENCE! HERE IS YOUR ESTIMATION:-
Good day, we would like to inform that you come under CATEGORY 2.
This category states that you are an individual with a Job, with a High
or Medium salary, with a low or medium Credit Score and with a Young age.

The estimated Loan that you can borrow:- $105000
```
### TEST CASE 3
```
----------|WELCOME TO AUTOMATED LOAN ESTIMATION SYSTEM|----------
Kindly answer the following questions to start:-

Enter your age(ex. 34)
Please enter a value between (1-98): 45

Are you a male/female?
1.Male
2.Female
Enter your choice(1/2):-

1

What is your highest level of education?
1. High School
2. Bachelors
3. Masters
Enter your choice(1/2/3):-

3

What does your credit score looking like?
Enter your choice(eg. 740):-
Please enter a value between (305-895): 740

What is your marital status?
1. Single
2. Married
3. Divorced
4. Seperated
Enter your choice(1/2/3/4):-

1

Do you have a Job?
1.Yes
2.No
Enter your choice(1/2):-

2

Do you have any other sources of income?
Enter amount(eg. 7500):-
Please enter a value between ($5-$9995): 8000

THANK YOU FOR YOUR PATIENCE! HERE IS YOUR ESTIMATION:-
Good day, we would like to inform that you come under CATEGORY 6.
This category states that you are an individual with no Job, with High or Medium other income,
with a Low/Medium Credit Score and with a Young/Mid/Elderly age.

The estimated Loan that you can borrow:- $55000
```
### ABOUT PROJECT
This Project was done as a part of CS:514 Applied Artificial Intelligence at UIC
