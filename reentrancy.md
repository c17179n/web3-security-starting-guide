Атака повторного входа, вероятно, самая известная уязвимость Ethereum, удивила всех, когда была обнаружена впервые. Впервые он был обнародован во время многомиллионного ограбления, которое привело к хард-форку Ethereum. Повторный вход происходит, когда внешним вызовам контракта разрешено выполнять новые вызовы вызывающего контракта до завершения первоначального выполнения. Для функции это означает, что состояние контракта может измениться в середине его выполнения в результате вызова недоверенного контракта или использования низкоуровневой функции с внешним адресом.Пример:

 Смарт-контракт отслеживает баланс ряда внешних адресов и позволяет пользователям извлекать средства с помощью своей публичной функции снятия().
  Вредоносный смарт-контракт использует функцию remove() для получения всего своего баланса.
 Контракт-жертва выполняет низкоуровневую функцию call.value(amount)(), чтобы отправить эфир вредоносному контракту перед обновлением баланса вредоносного контракта.
  У вредоносного контракта есть оплачиваемая функция fallback(), которая принимает средства, а затем вызывает обратно функциюdraw() контракта-жертвы.
 Это второе выполнение запускает перевод средств: помните, баланс вредоносного контракта еще не обновился с момента первого вывода средств. В результате вредоносный контракт успешно выводит весь свой баланс во второй раз.

Пример кода:  
эта функция содержит функцию, уязвимую для повторной атаки. Когда низкоуровневая функция call() отправляет эфир на адрес msg.sender, она становится уязвимой; если адрес является смарт-контрактом, платеж активирует резервную функцию с тем, что осталось от транзакционного газа
```
function withdraw(uint _amount) {
	require(balances[msg.sender] >= _amount);
	msg.sender.call.value(_amount)();
	balances[msg.sender] -= _amount;
}
```