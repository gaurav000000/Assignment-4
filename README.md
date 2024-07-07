# Assignment-4
Procedures
1) update charges per hour
delimiter //
create procedure update charges per.
•hour (
in P driver name varchar (50), in p_change percentage float (5),
in P.change_status enum('i', "d'), out pout int (5)
begin declare y.driver id int;
declare y current charges decimal (6, 2); declare ynew charges decimal(6, 2);
-- Check if the driver exists select driver id into v_driver_id
from driver details
where driver name = p driver name;
if vdriver_
id is not null then
if p.change_status = 'i' then
-- Increase charges per_hour
select charges per hour into y current charges
from vehicle details
where driver id = v driver id;
set v new charges = y current charges * (1 + (P_change percentage / 100));
update vehicle details
set charges per_hour = v. new charges
where driver id = v driver id;
if y driver id is not null then
if change status = 'i' then
-- Increase charges per hour select charges per hour into y current charges from vehicle details
where driver id = y driver id;
set v new charges = v current charges * (1 tuR. change percentage / 100));
update vehicle details
set charges per hour = x new charges
where driver id = y dciver id;
set Rout = 1; -- Increased charges
elseif change status = 'd' then
-- Decrease charges per hour select charges per heur into v current charges from vehicle details
where driver id = y driver id;
set v new charges = y current charges * (1 - (R. change percentage / 100));
update vehicle details
set charges per bour = v new charges
where driver id = y driver 7d;
set Rout = 2; -- Decreased charges
else
set Pout = -1; -- Invalid change status
end if;
else
set pout = -1; -- Driver not found
end if;
end //


delimiter ;
2) recharge wallet
delimiter //
create procedure recharge wallet( in Rcustomer id int,
¿nout P._amount decimal(6, 2)
begin
declare vexisting wallet decimal (6, 2);
select wallet into existing wallet from customer details
where customer id = p.customer id;
if v existing wallet is not null then
if p_amount ＜= 8 then
set P_amount = -2;
else
set P_amount = Ramount * 0.95;
set P_amount = pamount * 0.975;
update customer details
set wallet = wallet + P_amount
where customer.
id = Rcustomer id;
end if;
else
set pamount = -1;
end if;
end //
delimiter ;


3) book_ cab
delimiter //
create procedure book.cab
in p customer name varchar (50), in P.source varchar (50), in P.destination varchar(50),
in Ptype of travel enum(sol, pool',
in Pnumber of persons int, out pout varchar (100)
begin
declare y customer.
id int;
declare v driver id int;
declare booking.
id varchar (10);
-- Check if the customer exists select customer_ id into ycustomer_id from customer details where customer.
_name = Rcustomer name;
if v customer id is null then
set p_out = 'Please register to book a cab';
else
-- Allocate driver
set v driver_id = allocate
driver (psource, p destination, P_type of travel, P_ number.
of _persons;
if variver.
id = -1 then
set pout = 'Cabs are unavailable for the number of persons selected by you';
elseif y driver id = -2 then
set p_out = 'Currently no cabs are available';
else
-- Generate new booking id
   begin
declare y customer id int; declare v.driver_id int;
declare v booking id varchar (10) ;
-- Check if the customer exists select customer id into v. customer. id from customer details
where customer name = p.customer.name;
if v customer id is null then
set p_out = 'Please register to book a cab';
else
-- Allocate driver
set _driver_id = allocate driver (P_source, P_destination, P._type_of_travel, P_number_of_persons);
if y driver_id = -1 then
set p_out = 'Cabs are unavailable for the number of persons selected by you';
elseif y driver id = -2 then
set p_out = 'Currently no cabs are available';
else
-- Generate new booking id
set y_booking_id = generate_new_id('B');
-- Insert new booking details
insert into booking details (booking_id, customer id, driver id, source, destination, type of travel, number of persons, bookang_status) values (v booking id, v customer id, v. driver id, Psource, Pdestination, Ptype of travel, P_number of persons, "waiting");
set p_out = 'Booking Successful!';
end if;
end if;
end //
delimiter ;

4) update coupons
delimiter //
create procedure update coupons ()
begin
declare v current date date; declare v expiry date date;
-- Get current date
set v current date = current date();
-- Update expiry date for all coupons update coupon details
set expiry date = case
when expiry date ‹ v current date then
case
when datediff (v current date, expiry date) › 270 then date add (v current date, interval 70 day) else date add (v. current date, interval 30 day)
else
end
case
when datediff(expiry date, v current date) <= 10 then date add (expiry date, interval 15 day) when datediff (expiry date, current date) <= 20 then date add (expiry date, interval 25 day)
else expiry date
end
end;
end //
delimiter;


5) calculate bill
delimiter //
create procedure calculate bill
in Pbooking..
id varchar (10),
in Piourney time int, in Pwaiting time int,
in p coupon code varchar (50), in Ppayment type enum 'cash' , 'wallet', 'upi"), out R credit points int, out p_bill decimal (6,2)
begin
declare v. charges per hour decimal (6,2); declare v.journey_charge decimal (6,2); declare v waiting charge decimal (6,2);
declare total fine_
amount decimal (6,2);
declare vtotal bill decimal (6,2); declare v. coupon discount decimal (6,2);
declare v new_payment.
_id varchar (10);
-- Check if booking already paid
if exists (select 1 from payment details where booking id = Pl
booking id) then
set p bill = -1;
return;
end if;
-- Check if booking exists and completed select charges per.
hour into y charges per hour
from booking details b
join vehicle details v on b.driver id = v. driver id
where b.booking id = pbooking id and b.booking status = 'completed';
if v charges per hour is null then
-- Check if booking exists and completed select charges per hour into y charges per hour from booking details b
join vehicle details v on b.driver id = v. driver id
where b.booking id = R booking id and b.booking status = 'completed';
if v charges per hour is null then
set P.bill = -2;
return;
end if;
-- Calculate journey charge
set vlourney charge = Pourney
time * v charges per..
hour;
-- Calculate waiting charge
set v waiting charge = p waiting time * v charges per hour;
-- Calculate total fine amount for recent cancelled bookings
set v total_fine_amount = (
select sum (vcharges per hour * 0.01)
from booking details where booking id <> p.
_booking id
and booking_ status = 'cancelled'
and date_of.
_booking › ( select max (date_of.
_booking)
from booking details
where booking status = 'completed'
) ;
-- Calculate total bill amount
set tota-_0111 = V.
Journey charge + ywaiting charge + total fine amount;
-- Apply coupon discount if coupon code is provided if coupon code is not null then
   -- Apply coupon discount if coupon code is provided if p coupon code is not null then
set vcoupon discount = get discount (v. total bill, R._coupon_code);
end if;
set v_total_bill = Vtotal bill - y coupon discount;
-- Generate new payment id
set x new payment_ id = generate new id('p');
-- Insert new payment details insert into payment details (payment.
id, booking id, journey duration, waiting duration, coupon code, fare, payment_type)
values (v new payment id, booking id, P journey time, R waiting time, R coupon code, V total_bill, P_payment _type);
-- Deduct credit points if customer has more than 50 select credit points into Pcredit points from customer details
where customer_id = (
select customer id from booking details
where booking id = P_booking id
if P_credit points › 50 then
set v_tota-_bill = v_total_bill - floor(p. credit points / 10);
if v total bill < 0 then
set v_total_bill = 0;
end if;
update customer details
set credit points = credit points - (p.credit points - P_bill)
where customer_id = (
select customer_id from booking details
where booking id = p booking_ id
end


6) delete booking
delimiter //
create procedure delete booking ( in Pbooking id varchar(10), out p_status int (5)
begin
declare exit handler for salexception begin
set P_status = -1;
end;
delete from booking details where booking id = P booking id;
set Pustatus = 1;
end //
delimiter ;
