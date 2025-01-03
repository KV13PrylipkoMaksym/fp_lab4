
<p align="center"><b>МОНУ НТУУ КПІ ім. Ігоря Сікорського ФПМ СПіСКС</b></p>
<p align="center">
<b>Звіт з лабораторної роботи 4</b><br/>
"Функції вищого порядку та замикання"<br/>
дисципліни "Вступ до функціонального програмування"
</p>
<p align="right"><b>Студентка</b>: Давидюк Микола Юрійович КВ-12</p>
<p align="right"><b>Рік</b>: 2024</p>

## Загальне завдання
1. Переписати функціональну реалізацію алгоритму сортування з лабораторної
роботи 3 з такими змінами:
використати функції вищого порядку для роботи з послідовностями (де це
доречно);
додати до інтерфейсу функції (та використання в реалізації) два ключових
параметра: key та test , що працюють аналогічно до того, як працюють
параметри з такими назвами в функціях, що працюють з послідовностями. При
цьому key має виконатись мінімальну кількість разів.
2. Реалізувати функцію, що створює замикання, яке працює згідно із завданням за
варіантом (див. п 4.1.2). Використання псевдо-функцій не забороняється, але, за
можливості, має бути мінімізоване.

## Варіант першої частини - 7 (15 за списком)
Алгоритм сортування Шелла за незменшенням.
## Лістинг реалізації першої частини завдання
```lisp
(defun shell-sort (lst n gap i key test)
  (if (>= gap 1)
      (if (< i n)
          (let ((j i))
            (if (and (>= j gap)
                     (funcall test (funcall key (nth j lst))
                              (funcall key (nth (- j gap) lst))))
                (progn
                  (rotatef (nth j lst) (nth (- j gap) lst))
                  (shell-sort lst n gap (- j gap) key test))
                (shell-sort lst n gap (+ i 1) key test)))
          (shell-sort lst n (floor (/ gap 2)) 0 key test))
      lst))

(defun shell-sort-wrapper (lst &key (key #'identity) (test #'<))
  (let ((new-lst (copy-list lst))) 
    (let ((n (length lst)))
      (shell-sort new-lst n (floor (/ n 2)) 0 key test))))
```

### Тестові набори та утиліти першої частини
```lisp
(defun check-shell-sort (test-name input expected &key (key #'identity) (test #'<))
  "Helper function to run individual test cases."
  (let ((result (shell-sort-wrapper input :key key :test test)))
    (if (equal result expected)
        (format t "~a passed. Result: ~a~%" test-name result)
        (format t "~a failed.~%  Input: ~a~%  Expected: ~a~%  Got: ~a~%" test-name input expected result))))

 (defun test-shell-sort ()
  "Run a series of tests for shell-sort-wrapper."
  (format t "Start testing shell-sort-wrapper function~%")
  (check-shell-sort "Test 1" '(5 3 8 6 2 7 4 1) '(1 2 3 4 5 6 7 8))
  (check-shell-sort "Test 2" '(10 9 8 7 6 5 4 3 2 1) '(1 2 3 4 5 6 7 8 9 10))
  (check-shell-sort "Test 3" '(1 1 1 1 1) '(1 1 1 1 1))
  (check-shell-sort "Test 4" '(100 -10 0 50 -50) '(-50 -10 0 50 100))
  (check-shell-sort "Test 5" '(-1 -2 -3 -4 -5) '(-5 -4 -3 -2 -1))
  (check-shell-sort "Test 6" '(2 2 2 2 1 1 1 1 3 3 3 3) '(1 1 1 1 2 2 2 2 3 3 3 3))
  (check-shell-sort "Test 7" '((1 . "a") (3 . "b") (2 . "c")) '((1 . "a") (2 . "c") (3 . "b"))
                    :key #'car :test #'<)
  (check-shell-sort "Test 8" '((3 . 9) (1 . 2) (2 . 5)) '((1 . 2) (2 . 5) (3 . 9))
                    :key #'cdr :test #'<)
  (check-shell-sort "Test 9" '(5 4 3 2 1) '(5 4 3 2 1)
                    :test #'=)
  (format t "Testing completed~%"))
```
### Тестування першої частини
```lisp
CL-USER>  (test-shell-sort)
Start testing shell-sort-wrapper function
Test 1 passed. Result: (1 2 3 4 5 6 7 8)
Test 2 passed. Result: (1 2 3 4 5 6 7 8 9 10)
Test 3 passed. Result: (1 1 1 1 1)
Test 4 passed. Result: (-50 -10 0 50 100)
Test 5 passed. Result: (-5 -4 -3 -2 -1)
Test 6 passed. Result: (1 1 1 1 2 2 2 2 3 3 3 3)
Test 7 passed. Result: ((1 . a) (2 . c) (3 . b))
Test 8 passed. Result: ((1 . 2) (2 . 5) (3 . 9))
Test 9 passed. Result: (5 4 3 2 1)
Testing completed
```
## Варіант другої частини - 3 (15 за списком)
Написати функцію add-next-fn , яка має один ключовий параметр — функцію
transform . add-next-fn має повернути функцію, яка при застосуванні в якості
першого аргументу mapcar разом з одним списком-аргументом робить наступне: кожен
елемент списку перетворюється на точкову пару, де в комірці CAR знаходиться значення
поточного елемента, а в комірці CDR знаходиться значення наступного елемента списку.
Якщо функція transform передана, тоді значення поточного і наступного елементів, що
потраплять у результат, мають бути змінені згідно transform . transform має
виконатись мінімальну кількість разів.

```lisp
CL-USER> (mapcar (add-next-fn) '(1 2 3))
((1 . 2) (2 . 3) (3 . NIL))
CL-USER> (mapcar (add-next-fn :transform #'1+) '(1 2 3))
((2 . 3) (3 . 4) (4 . NIL))
```

## Лістинг реалізації другої частини завдання
```lisp
(defun add-next-fn (&key (transform 'identity))
  (let ((prev-element nil))
  (lambda (current)
    (if prev-element  
         (progn (rplacd prev-element (funcall transform current))
                (setf prev-element (cons (cdr prev-element) nil)))
         (setf prev-element (cons (funcall transform current) nil)))
      )))
```
### Тестові набори та утиліти другої частини 
```lisp
 (defun run-next-fn-test (input transform expected-result test-description)
  (let ((result (if(not(null transform))(mapcar (add-next-fn :transform transform) input)(mapcar (add-next-fn) input))))
    (if (equal result expected-result)
        (format t "~A: success.~%" test-description)
        (format t "~A: failed! ~%Expected: ~A~%Got: ~A~%" test-description expected-result result))))

(defun add-next-fn-test ()
  (format t "Testing add-next-fn...~%")
  (run-next-fn-test '(1 2 3) nil '((1 . 2) (2 . 3) (3 . NIL)) "1 ")
  (run-next-fn-test '(1 2 3) #'1+ '((2 . 3) (3 . 4) (4 . NIL)) "2 ")
  (run-next-fn-test '() nil '() "3 ")
  (run-next-fn-test '(1) nil '((1 . NIL)) "4 ")
  (run-next-fn-test '(1) #'1+ '((2 . NIL)) "5 ")
  (format t "Test completed~%"))
```
### Тестування другої частини 
```lisp
Testing add-next-fn...
1 : success.
2 : success.
3 : success.
4 : success.
5 : success.
Test completed
```
