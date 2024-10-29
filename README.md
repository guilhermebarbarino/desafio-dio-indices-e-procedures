# desafio-dio-indices-e-procedures

---

## **Parte 1 - Criação de Índices**

Aqui, precisamos criar índices nas tabelas do cenário `company` para acelerar as consultas específicas. Lembrando que índices são particularmente úteis para colunas frequentemente usadas em `JOIN`, `WHERE` ou `ORDER BY`. A seguir, temos as queries pedidas e os índices correspondentes:

### **Consultas e Índices Criados**

1. **Qual o departamento com maior número de pessoas?**  
   **Consulta SQL:**
   ```sql
   SELECT D.Dname, COUNT(E.Ssn) AS total_funcionarios
   FROM Employee E
   JOIN Department D ON E.Dno = D.Dnumber
   GROUP BY D.Dname
   ORDER BY total_funcionarios DESC
   LIMIT 1;
   ```

   **Índice Criado:**
   ```sql
   CREATE INDEX idx_employee_department ON Employee(Dno);
   ```

   *Motivo:* A coluna `Dno` é usada para unir as tabelas `Employee` e `Department`. O índice irá acelerar a execução do `JOIN`.

---

2. **Quais são os departamentos por cidade?**  
   **Consulta SQL:**
   ```sql
   SELECT D.Dname, L.Dlocation
   FROM Department D
   JOIN Dept_Locations L ON D.Dnumber = L.Dnumber;
   ```

   **Índice Criado:**
   ```sql
   CREATE INDEX idx_department_location ON Dept_Locations(Dnumber);
   ```

   *Motivo:* A coluna `Dnumber` é utilizada para realizar o `JOIN` entre `Department` e `Dept_Locations`.

---

3. **Relação de empregados por departamento:**  
   **Consulta SQL:**
   ```sql
   SELECT E.Fname, E.Lname, D.Dname
   FROM Employee E
   JOIN Department D ON E.Dno = D.Dnumber
   ORDER BY D.Dname;
   ```

   **Índice Criado:**
   ```sql
   CREATE INDEX idx_employee_department_name ON Employee(Dno);
   CREATE INDEX idx_department_name ON Department(Dname);
   ```

   *Motivo:* Os índices em `Dno` e `Dname` garantem uma busca mais rápida tanto na junção quanto na ordenação por nome do departamento.

---

### **README (Motivo da Criação dos Índices)**
```text
# Descrição do Projeto

Este projeto visa otimizar o banco de dados de uma empresa através da criação de índices e uso de procedures. Os índices foram criados para colunas frequentemente utilizadas em consultas e joins, acelerando assim a recuperação dos dados. Apenas colunas realmente necessárias foram indexadas, evitando sobrecarga desnecessária.

Índices criados:
1. idx_employee_department: Para acelerar a junção entre empregados e departamentos.
2. idx_department_location: Para melhorar a consulta de departamentos por localização.
3. idx_employee_department_name e idx_department_name: Para junção e ordenação dos funcionários por departamento.
```

---

## **Parte 2 - Procedure para Manipulação de Dados**

Aqui, vamos criar uma procedure que permite inserção, remoção e atualização de dados com base em uma variável de controle. Abaixo está o código SQL com uma procedure exemplo para o banco de dados `company` e uma para o `e-commerce`.

### **Procedure para Manipular Dados da Universidade/Company:**
```sql
DELIMITER //

CREATE PROCEDURE ManageEmployeeData(
    IN action INT, 
    IN emp_ssn INT, 
    IN fname VARCHAR(50), 
    IN lname VARCHAR(50), 
    IN salary DECIMAL(10, 2)
)
BEGIN
    CASE action
        WHEN 1 THEN -- Inserção
            INSERT INTO Employee (Ssn, Fname, Lname, Salary) 
            VALUES (emp_ssn, fname, lname, salary);
        WHEN 2 THEN -- Atualização
            UPDATE Employee 
            SET Fname = fname, Lname = lname, Salary = salary 
            WHERE Ssn = emp_ssn;
        WHEN 3 THEN -- Remoção
            DELETE FROM Employee WHERE Ssn = emp_ssn;
        ELSE
            SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Ação inválida';
    END CASE;
END //

DELIMITER ;
```

### **Procedure para Manipular Dados do E-commerce:**
```sql
DELIMITER //

CREATE PROCEDURE ManageProductData(
    IN action INT, 
    IN prod_id INT, 
    IN prod_name VARCHAR(100), 
    IN prod_price DECIMAL(10, 2)
)
BEGIN
    CASE action
        WHEN 1 THEN -- Inserção
            INSERT INTO Produto (idProduto, nomeProduto, preco) 
            VALUES (prod_id, prod_name, prod_price);
        WHEN 2 THEN -- Atualização
            UPDATE Produto 
            SET nomeProduto = prod_name, preco = prod_price 
            WHERE idProduto = prod_id;
        WHEN 3 THEN -- Remoção
            DELETE FROM Produto WHERE idProduto = prod_id;
        ELSE
            SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Ação inválida';
    END CASE;
END //

DELIMITER ;
```

### **Chamada das Procedures:**
```sql
-- Exemplo de inserção de funcionário
CALL ManageEmployeeData(1, 12345, 'João', 'Silva', 5000.00);

-- Exemplo de atualização de produto
CALL ManageProductData(2, 1, 'Notebook Gamer', 4999.99);
```

---
