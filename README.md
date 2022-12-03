# ds
data structure
q-1)

declare 
	eno number;
	empname varchar2(10);
	designation char(9);
	cnt number;
begin
	eno:= &eno;
	select count(*) into cnt from emp where empno=eno;
	if cnt=1 then
		select ename,job into empname,designation from emp where empno=eno;
		dbms_output.put_line(empname);
		dbms_output.put_line(designation);
	else
		dbms_output.put_line('Record Not Found');
	end if;
end;
/ 

============================================================================
q-2)

begin
	dbms_output.put_line('Empno  |  Ename  |  Job  |  sal  |  Manager Name');
	for a in (select empno,ename,job,sal,mgr from emp) 
	loop
		for b in(select ename from emp where empno= nvl(a.mgr,a.empno))
		loop
		if a.ename=b.ename then
			dbms_output.put_line(a.empno || '      ' || a.ename || '      '|| a.job || '      '|| a.sal || '      ' || '-');
		else
			dbms_output.put_line(a.empno || '      ' || a.ename || '      '|| a.job || '      '|| a.sal || '      ' || b.ename);
		end if;
		end loop;
	end loop;
end;
/	

==========================================================

q-3)
	
declare
	cnt number;
begin
	for a in (select deptno,count(*) as cnt from emp group by deptno)
	loop
		if a.cnt>0 then
			for b in(select dname,loc from dept where deptno=a.deptno)
			loop
				dbms_output.put_line('Deptno: ' || RPAD(a.deptno,10) || '  ' || 'Department Name: ' || RPAD(b.dname,10) || '   ' || 'Location: ' || b.loc);
				dbms_output.put_line('Empno      Name      Designation     Salary');
				dbms_output.put_line('--------------------------------------------');
			end loop;
			for c in(select empno,ename,job,sal from emp where deptno=a.deptno)
			loop
				dbms_output.put_line(RPAD(c.empno,10) || RPAD(c.ename,13) || RPAD(c.job,15) || RPAD(c.sal,10));
			end loop;	
				dbms_output.put_line('--------------------------------------------');
			for d in(select sum(sal) as total, max(sal) as High, min(sal) as low from emp where deptno=a.deptno)
			loop
				dbms_output.put_line( Chr(9) || Chr(9) || Chr(9) || RPAD('Total Salary',15) || d.total);
				dbms_output.put_line( Chr(9) || Chr(9) || Chr(9) || RPAD('Highest Salary',15) || d.High);
				dbms_output.put_line( Chr(9) || Chr(9) || Chr(9) || RPAD('Lowest Salary',15) || d.low); 

			end loop;
				dbms_output.put_line('--------------------------------------------'||Chr(10));
		end if;
	end loop;
end;
/

=======================================================================================================
q-4)

declare
	cursor c1 is select empno,ename,job,sal,mgr from emp;
	 c2 sys_refcursor;
	vempno emp.empno%type;
	vename emp.ename%type;
	vjob emp.job%type;
	vsal emp.sal%type;
	vmgr emp.mgr%type;
	vmgrname emp.ename%type;
	
begin
	dbms_output.put_line('Empno  |  Ename  |  Job  |  sal  |  Manager Name');
	open c1;
	loop
		fetch c1 into vempno,vename,vjob,vsal,vmgr;
		exit when c1%notfound;
		open c2 for select ename from emp where empno= nvl(vmgr,vempno);
		loop
			fetch c2 into vmgrname;
			exit when c2%notfound;
				if vename=vmgrname then
					dbms_output.put_line(vempno || '      ' || vename || '      '|| vjob || '      '|| vsal || '      ' || '-');
				else
					dbms_output.put_line(vempno || '      ' || vename || '      '|| vjob || '      '|| vsal || '      ' || vmgrname);
				end if;
		end loop;	
	end loop;
end;
/

=================================================================================================
q-5)

declare
	vcnt number;
	vdeptno dept.deptno%type;
	vdname dept.dname%type;
	vloc dept.loc%type;
	vempno emp.empno%type;
	vename emp.ename%type;
	vjob emp.job%type;
	vsal emp.sal%type;
	vtotal number;
	vhigh number;
	vlow number;
	cursor c1 is select deptno,count(*) from emp group by deptno;
	c2 sys_refcursor;
	c3 sys_refcursor;
	c4 sys_refcursor;

begin
	open c1;
	loop
		fetch c1 into vdeptno,vcnt;
		exit when c1%notfound;		
			if vcnt>0 then 
				open c2 for select dname,loc from dept where deptno=vdeptno;
				loop
					fetch c2 into vdname,vloc;
					exit when c2%notfound;
					dbms_output.put_line('Deptno: ' || RPAD(vdeptno,10) || '  ' || 'Department Name: ' || RPAD(vdname,10) || '   ' || 'Location: ' || vloc);
					dbms_output.put_line('Empno      Name      Designation     Salary');
					dbms_output.put_line('--------------------------------------------');
				end loop;
				open c3 for select empno,ename,job,sal from emp where deptno=vdeptno;
				loop
					fetch c3 into vempno,vename,vjob,vsal;
					exit when c3%notfound;
					dbms_output.put_line(RPAD(vempno,10) || RPAD(vename,13) || RPAD(vjob,15) || RPAD(vsal,10));
				end loop;
					dbms_output.put_line('--------------------------------------------');
				open c4 for select sum(sal), max(sal), min(sal) from emp where deptno=vdeptno;
				loop
					fetch c4 into vtotal,vhigh,vlow;
					exit when c4%notfound;
					dbms_output.put_line( Chr(9) || Chr(9) || Chr(9) || RPAD('Total Salary',15) || vtotal);
					dbms_output.put_line( Chr(9) || Chr(9) || Chr(9) || RPAD('Highest Salary',15) || vhigh);
					dbms_output.put_line( Chr(9) || Chr(9) || Chr(9) || RPAD('Lowest Salary',15) || vlow); 
				end loop;
					dbms_output.put_line('--------------------------------------------'||Chr(10));				 
			end if;
	end loop;
end;
/
================================================================================
q-6)

declare
	temp emp%rowtype;
	cursor c1 is select empno,ename,job,sal,mgr from emp;
	cursor c2(vempno number) is select ename from emp where empno=vempno;
	vmgrname emp.ename%type;
begin
	dbms_output.put_line('Empno  |  Ename  |  Job  |  sal  |  Manager Name');
	for temp in c1
	loop
		if c2%isopen then
		close c2;
		end if;
		open c2 (NVL(temp.mgr,temp.empno));
			loop
				fetch c2 into vmgrname ;
				exit when c2%notfound;
				if temp.ename = vmgrname then
					dbms_output.put_line(temp.empno || '      ' || temp.ename || '      '|| temp.job || '      '|| temp.sal || '      ' || '-');
				else
					dbms_output.put_line(temp.empno || '      ' || temp.ename || '      '|| temp.job || '      '|| temp.sal || '      ' || vmgrname);
				end if;
			end loop;
	end loop;
end;
/
========================================================================
q-7)

declare
	vcnt number;
	vdeptno dept.deptno%type;
	vdname dept.dname%type;
	vloc dept.loc%type;
	vempno emp.empno%type;
	vename emp.ename%type;
	vjob emp.job%type;
	vsal emp.sal%type;
	vtotal number;
	vhigh number;
	vlow number;
	temp dept%rowtype;
	emptemp emp%rowtype;
	cursor c1 is select deptno,dname,loc from dept where deptno in(select distinct deptno from emp);
	cursor c3(vdeptno number) is select empno,ename,job,sal from emp where deptno=vdeptno;
	cursor c4(vdeptno number) is select sum(sal),max(sal),min(sal) from emp where deptno=vdeptno;

begin
	for temp in c1 
	loop

					dbms_output.put_line('Deptno: ' || RPAD(temp.deptno,10) || '  ' || 'Department Name: ' || RPAD(temp.dname,10) || '   ' || 'Location: ' || temp.loc);
					dbms_output.put_line('Empno      Name      Designation     Salary');
					dbms_output.put_line('--------------------------------------------');
				if c3%isopen then
					close c3;
				end if;
				for emptemp in c3(temp.deptno)
				loop					
					dbms_output.put_line(RPAD(emptemp.empno,10) || RPAD(emptemp.ename,13) || RPAD(emptemp.job,15) || RPAD(emptemp.sal,10));
				end loop;
					dbms_output.put_line('--------------------------------------------');
				if c4%isopen then
					close c4;
				end if;				
				open c4(temp.deptno); 
				loop	
					fetch c4 into vtotal,vhigh,vlow;
					exit when c4%notfound;
					dbms_output.put_line( Chr(9) || Chr(9) || Chr(9) || RPAD('Total Salary',15) || vtotal);
					dbms_output.put_line( Chr(9) || Chr(9) || Chr(9) || RPAD('Highest Salary',15) || vhigh);
					dbms_output.put_line( Chr(9) || Chr(9) || Chr(9) || RPAD('Lowest Salary',15) || vlow); 
				end loop;
					dbms_output.put_line('--------------------------------------------'||Chr(10));				 
	end loop;
end;
/


============================================================================================

Q-8

declare
	cursor c1 is select e.ename,e.empno,e.deptno,d.dname from emp e,dept d where e.deptno=d.deptno and e.empno in(select distinct repid from customer);
	cursor c2(vrepid number) is select custid,name,city from customer where repid=vrepid;
	cursor c3(vcustid number) is select ordid,orderdate,total from ord where custid=vcustid;
	cursor c4_totalamt(vcustid number) is select sum(total) from ord where custid=vcustid;
	tempenmp emp%rowtype;
	tempcust customer%rowtype;
	tempord ord%rowtype;
	vtotal number;
begin
	for tempemp in c1
	loop
		dbms_output.put_line('Empno: ' || RPAD(tempemp.empno,10) || ' Name : ' || RPAD(tempemp.ename,15) || ' Deptno  : ' || RPAD(tempemp.deptno,13) || 'Deptname : ' || tempemp.dname );
		dbms_output.put_line('custid    Name     City   ');
		dbms_output.put_line('--------------------------------------------');
	for tempcust in c2(tempemp.empno)
	loop
		dbms_output.put_line(RPAD(tempcust.custid,8) || RPAD(tempcust.name,15) || RPAD(tempcust.city,10));
		dbms_output.put_line('Ordid     OrderDate     OrderAmount ');
		dbms_output.put_line('--------------------------------------------');
	for tempord in c3(tempcust.custid)
	loop
		dbms_output.put_line(RPAD(tempord.ordid,8) || RPAD(tempord.orderdate,15) || RPAD(tempord.total,10));
	end loop;  
		dbms_output.put_line('--------------------------------------------');
	if c4_totalamt%isopen then
		close c4_totalamt;
	end if;
	open c4_totalamt(tempcust.custid);
	loop
		fetch c4_totalamt into vtotal;
	exit when c4_totalamt%notfound;
		dbms_output.put_line('TOAL AMOUNT : ' ||  vtotal);
		dbms_output.put_line('--------------------------------------------');
	end loop;
	end loop;
	end loop;
end;
/

=======================================================================================

Q-9

declare
	cursor c1 is select e.ename,e.empno,e.deptno,d.dname from emp e,dept d where e.deptno=d.deptno and e.empno in(select distinct repid from customer);
	cursor c2(vrepid number) is select distinct custid,name from customer where repid=vrepid;
	cursor c3(vcustid number) is select count(ordid),sum(total) from ord where custid = vcustid;
	tempenmp emp%rowtype;
	tempcust customer%rowtype;
	tempord ord%rowtype;
	vtotal number;
	vcntord number;
begin
	for tempemp in c1
		loop
			dbms_output.put_line('Empno: ' || RPAD(tempemp.empno,10) || ' Name : ' || RPAD(tempemp.ename,15) || ' Deptno  : ' || RPAD(tempemp.deptno,7) || 'Deptname : ' || tempemp.dname );
			dbms_output.put_line('custid    Name            Total Orders     Total Amount  ');
			dbms_output.put_line('----------------------------------------------------------------');
		for tempcust in c2(tempemp.empno)
		loop
			if c3%isopen then
		close c3;
		end if;
		open c3(tempcust.custid);
		loop
			fetch c3 into vcntord,vtotal;
			exit when c3%notfound;
				dbms_output.put_line(RPAD(tempcust.custid,8) || RPAD(tempcust.name,20) || RPAD(vcntord,15) || RPAD(vtotal,10));
			end loop;
		dbms_output.put_line(chr(10));
	end loop;
	end loop;
end;
/

