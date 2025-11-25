
-- 1. Месторождение (Field)
CREATE TABLE well_equipment_failures.Field (
    field_id INT PRIMARY KEY,
    field_name VARCHAR(50) NOT NULL,
    status VARCHAR(20)
);

-- 2. Скважины (Wells)
CREATE TABLE well_equipment_failures.Wells (
    well_id INT PRIMARY KEY,
    field_id INT NOT NULL,
    well_number VARCHAR(10) NOT NULL UNIQUE,
    drilling_date DATE,
    depth DECIMAL(10,2),
    well_status VARCHAR(15),
    well_type VARCHAR(20),
    FOREIGN KEY (field_id) REFERENCES well_equipment_failures.Field(field_id)
);

-- 3. Производители оборудования (Manufacturers)
CREATE TABLE well_equipment_failures.Manufacturers (
    manufacturer_id INT PRIMARY KEY,
    manufacturer_name VARCHAR(50) NOT NULL,
    country VARCHAR(30),
    contact_info VARCHAR(100)
);

-- 4. Оборудование (Equipment)
CREATE TABLE well_equipment_failures.Equipment (
    equipment_id INT PRIMARY KEY,
    well_id INT NOT NULL,
    equipment_type VARCHAR(30) NOT NULL,
    model VARCHAR(40) NOT NULL,
    serial_number VARCHAR(30) UNIQUE,
    manufacturer_id INT NOT NULL,
    installation_date DATE NOT NULL,
    running_date DATE,
    condition_status VARCHAR(15),
    FOREIGN KEY (well_id) REFERENCES well_equipment_failures.Wells(well_id),
    FOREIGN KEY (manufacturer_id) REFERENCES well_equipment_failures.Manufacturers(manufacturer_id),
    CHECK (running_date >= installation_date OR running_date IS NULL)
);

-- 5. Параметры оборудования (EquipmentParameters)
CREATE TABLE well_equipment_failures.EquipmentParameters (
    parameter_id INT PRIMARY KEY,
    equipment_id INT NOT NULL,
    parameter_name VARCHAR(40) NOT NULL,
    parameter_value DECIMAL(12,4) NOT NULL,
    unit_of_measure VARCHAR(15),
    min_value DECIMAL(12,4),
    max_value DECIMAL(12,4),
    optimal_value DECIMAL(12,4),
    critical_deviation DECIMAL(12,4),
    FOREIGN KEY (equipment_id) REFERENCES well_equipment_failures.Equipment(equipment_id) ON DELETE CASCADE,
    CHECK (parameter_value BETWEEN min_value AND max_value OR min_value IS NULL OR max_value IS NULL)
);

-- 6. Типы отказов (FailureTypes)
CREATE TABLE well_equipment_failures.FailureTypes (
    failure_type_id INT PRIMARY KEY,
    failure_name VARCHAR(50) NOT NULL,
    description TEXT,
    criticality_level VARCHAR(10)
);

-- 7. Отказы оборудования (EquipmentFailures)
CREATE TABLE well_equipment_failures.EquipmentFailures (
    failure_id INT PRIMARY KEY,
    equipment_id INT NOT NULL,
    failure_type_id INT NOT NULL,
    failure_date TIMESTAMP NOT NULL,
    detection_method VARCHAR(20),
    description TEXT,
    parameter_values_before_failure TEXT,
    FOREIGN KEY (equipment_id) REFERENCES well_equipment_failures.Equipment(equipment_id) ON DELETE CASCADE,
    FOREIGN KEY (failure_type_id) REFERENCES well_equipment_failures.FailureTypes(failure_type_id)
);

-- 8. Ремонтные работы (Repairs)
CREATE TABLE well_equipment_failures.Repairs (
    repair_id INT PRIMARY KEY,
    failure_id INT NOT NULL,
    start_date TIMESTAMP NOT NULL,
    end_date TIMESTAMP,
    repair_type VARCHAR(20),
    cost DECIMAL(12,2),
    repair_status VARCHAR(15),
    FOREIGN KEY (failure_id) REFERENCES well_equipment_failures.EquipmentFailures(failure_id) ON DELETE CASCADE,
    CHECK (end_date >= start_date OR end_date IS NULL)
);

-- 9. Запчасти (SpareParts)
CREATE TABLE well_equipment_failures.SpareParts (
    part_id INT PRIMARY KEY,
    part_name VARCHAR(50) NOT NULL,
    manufacturer VARCHAR(50),
    part_number VARCHAR(20) UNIQUE,
    stock_quantity INT NOT NULL,
    min_stock_level INT NOT NULL,
    storage_location VARCHAR(30)
);

-- 10. Использование запчастей (PartsUsage)
CREATE TABLE well_equipment_failures.PartsUsage (
    usage_id INT PRIMARY KEY,
    repair_id INT NOT NULL,
    part_id INT NOT NULL,
    quantity_used INT NOT NULL,
    usage_date DATE NOT NULL,
    unit_cost DECIMAL(10,2),
    FOREIGN KEY (repair_id) REFERENCES well_equipment_failures.Repairs(repair_id) ON DELETE CASCADE,
    FOREIGN KEY (part_id) REFERENCES well_equipment_failures.SpareParts(part_id) ON DELETE CASCADE
);

-- Заполнение таблиц:
-- 1. Заполнение таблицы field (Месторождение) - 1000+ записей
INSERT INTO well_equipment_failures.Field (field_id, field_name, status)
SELECT 
    seq as field_id,
    'Field_' || seq as field_name,
    CASE 
        WHEN (seq % 5) = 0 THEN 'active'
        WHEN (seq % 5) = 1 THEN 'development'
        WHEN (seq % 5) = 2 THEN 'exploration'
        WHEN (seq % 5) = 3 THEN 'conservation'
        ELSE 'closed'
    END as status
FROM generate_series(1, 1001) seq;

-- Заполнение таблицы Wells (более 1000 скважин)

INSERT INTO well_equipment_failures.Wells (well_id, field_id, well_number, drilling_date, depth, well_status, well_type)
SELECT 
    seq as well_id,
    ((seq - 1) % 1001) + 1 as field_id,  -- Исправлено: field_id от 1 до 1001
    'W' || LPAD(seq::text, 4, '0') as well_number,
    DATE '2020-01-01' + ((seq % 1000) * INTERVAL '1 day') as drilling_date,
    ROUND(1000 + (seq % 9000)::numeric, 2) as depth,
    CASE (seq % 6)
        WHEN 0 THEN 'drilling'
        WHEN 1 THEN 'producing'
        WHEN 2 THEN 'injection'
        WHEN 3 THEN 'observation'
        WHEN 4 THEN 'abandoned'
        ELSE 'suspended'
    END as well_status,
    CASE (seq % 4)
        WHEN 0 THEN 'vertical'
        WHEN 1 THEN 'horizontal'
        WHEN 2 THEN 'directional'
        ELSE 'multilateral'
    END as well_type
FROM generate_series(1, 1500) seq;


-- Заполнение таблицы Manufacturers (более 1000 производителей)
INSERT INTO well_equipment_failures.Manufacturers (manufacturer_id, manufacturer_name, country, contact_info)
SELECT 
    manufacturer_id,
    'Производитель ' || manufacturer_id,
    CASE (manufacturer_id % 10)
        WHEN 0 THEN 'Россия'
        WHEN 1 THEN 'США'
        WHEN 2 THEN 'Германия'
        WHEN 3 THEN 'Китай'
        WHEN 4 THEN 'Япония'
        WHEN 5 THEN 'Франция'
        WHEN 6 THEN 'Италия'
        WHEN 7 THEN 'Канада'
        WHEN 8 THEN 'Великобритания'
        ELSE 'Южная Корея'
    END,
    'contact' || manufacturer_id || '@company.com'
FROM generate_series(1, 1100) as manufacturer_id;

-- Заполнение таблицы Equipment (более 1000 единиц оборудования)
INSERT INTO well_equipment_failures.Equipment (equipment_id, well_id, equipment_type, model, serial_number, manufacturer_id, installation_date, running_date, condition_status)
SELECT 
    equipment_id,
    (equipment_id % 1500) + 1,
    CASE (equipment_id % 6)
        WHEN 0 THEN 'pump'
        WHEN 1 THEN 'motor'
        WHEN 2 THEN 'sensor'
        WHEN 3 THEN 'valve'
        WHEN 4 THEN 'compressor'
        ELSE 'generator'
    END,
    'MODEL-' || (equipment_id % 100 + 1),
    'SN-' || LPAD(equipment_id::text, 6, '0'),
    (equipment_id % 1100) + 1,
    CURRENT_DATE - INTERVAL '1 day' * (equipment_id % 2000),
    CURRENT_DATE - INTERVAL '1 day' * (equipment_id % 1800),
    CASE (equipment_id % 5)
        WHEN 0 THEN 'excellent'
        WHEN 1 THEN 'good'
        WHEN 2 THEN 'satisfactory'
        WHEN 3 THEN 'poor'
        ELSE 'critical'
    END
FROM generate_series(1, 2000) as equipment_id;

-- Заполнение таблицы FailureTypes (более 1000 типов отказов)
INSERT INTO well_equipment_failures.FailureTypes (failure_type_id, failure_name, description, criticality_level)
SELECT 
    failure_type_id,
    'Тип отказа ' || failure_type_id,
    'Описание типа отказа ' || failure_type_id || '. Это автоматически сгенерированное описание для демонстрационных целей.',
    CASE (failure_type_id % 4)
        WHEN 0 THEN 'low'
        WHEN 1 THEN 'medium'
        WHEN 2 THEN 'high'
        ELSE 'critical'
    END
FROM generate_series(1, 1200) as failure_type_id;

-- Заполнение таблицы EquipmentFailures (более 1000 отказов)
INSERT INTO well_equipment_failures.EquipmentFailures (failure_id, equipment_id, failure_type_id, failure_date, detection_method, description, parameter_values_before_failure)
SELECT 
    failure_id,
    (failure_id % 2000) + 1,
    (failure_id % 1200) + 1,
    CURRENT_TIMESTAMP - INTERVAL '1 hour' * (failure_id % 8760),
    CASE (failure_id % 4)
        WHEN 0 THEN 'automatic'
        WHEN 1 THEN 'manual'
        WHEN 2 THEN 'inspection'
        ELSE 'sensor'
    END,
    'Отказ оборудования ' || failure_id || '. Обнаружен в процессе эксплуатации. Требуется диагностика и ремонт.',
    '{"temperature": ' || ROUND((50 + RANDOM() * 100)::numeric, 2) || ', "pressure": ' || ROUND((10 + RANDOM() * 50)::numeric, 2) || ', "vibration": ' || ROUND((1 + RANDOM() * 10)::numeric, 2) || '}'
FROM generate_series(1, 2500) as failure_id;

-- Заполнение таблицы Repairs (более 1000 ремонтов)
INSERT INTO well_equipment_failures.Repairs (repair_id, failure_id, start_date, end_date, repair_type, cost, repair_status)
SELECT 
    repair_id,
    (repair_id % 2500) + 1,
    CURRENT_TIMESTAMP - INTERVAL '1 day' * (repair_id % 365),
    CASE 
        WHEN repair_id % 10 != 0 THEN CURRENT_TIMESTAMP - INTERVAL '1 day' * (repair_id % 30)
        ELSE NULL
    END,
    CASE (repair_id % 4)
        WHEN 0 THEN 'emergency'
        WHEN 1 THEN 'scheduled'
        WHEN 2 THEN 'preventive'
        ELSE 'overhaul'
    END,
    ROUND((1000 + RANDOM() * 50000)::numeric, 2),
    CASE 
        WHEN repair_id % 10 = 0 THEN 'planned'
        WHEN repair_id % 10 = 1 THEN 'in_progress'
        WHEN repair_id % 10 = 2 THEN 'cancelled'
        ELSE 'completed'
    END
FROM generate_series(1, 3000) as repair_id;

-- Заполнение таблицы SpareParts
INSERT INTO well_equipment_failures.SpareParts (part_id, part_name, manufacturer, part_number, stock_quantity, min_stock_level, storage_location)
SELECT 
    part_id,
    'Запчасть ' || part_id,
    'Производитель ' || (part_id % 50 + 1),
    'PN-' || LPAD(part_id::text, 6, '0'),
    (part_id % 1000),
    (part_id % 100),
    CASE (part_id % 10)
        WHEN 0 THEN 'Склад A'
        WHEN 1 THEN 'Склад B'
        WHEN 2 THEN 'Склад C'
        WHEN 3 THEN 'Склад D'
        WHEN 4 THEN 'Склад E'
        WHEN 5 THEN 'Склад F'
        WHEN 6 THEN 'Склад G'
        WHEN 7 THEN 'Склад H'
        WHEN 8 THEN 'Склад I'
        ELSE 'Склад J'
    END
FROM generate_series(1, 1500) as part_id;

-- Заполнение таблицы EquipmentParameters (более 1000 параметров)
INSERT INTO well_equipment_failures.EquipmentParameters (parameter_id, equipment_id, parameter_name, parameter_value, unit_of_measure, min_value, max_value, optimal_value, critical_deviation)
SELECT 
    parameter_id,
    (parameter_id % 2000) + 1,
    CASE (parameter_id % 6)
        WHEN 0 THEN 'temperature'
        WHEN 1 THEN 'pressure'
        WHEN 2 THEN 'vibration'
        WHEN 3 THEN 'voltage'
        WHEN 4 THEN 'current'
        ELSE 'rpm'
    END,
    ROUND((50 + RANDOM() * 200)::numeric, 4),
    CASE (parameter_id % 6)
        WHEN 0 THEN '°C'
        WHEN 1 THEN 'bar'
        WHEN 2 THEN 'mm/s'
        WHEN 3 THEN 'V'
        WHEN 4 THEN 'A'
        ELSE 'rpm'
    END,
    ROUND((30 + RANDOM() * 50)::numeric, 4),
    ROUND((150 + RANDOM() * 100)::numeric, 4),
    ROUND((80 + RANDOM() * 70)::numeric, 4),
    ROUND((10 + RANDOM() * 20)::numeric, 4)
FROM generate_series(1, 5000) as parameter_id;

-- Заполнение таблицы PartsUsage (более 1000 использований запчастей)
INSERT INTO well_equipment_failures.PartsUsage (usage_id, repair_id, part_id, quantity_used, usage_date, unit_cost)
SELECT 
    usage_id,
    (usage_id % 3000) + 1,
    (usage_id % 1500) + 1,
    (usage_id % 10) + 1,
    CURRENT_DATE - INTERVAL '1 day' * (usage_id % 365),
    ROUND((10 + RANDOM() * 500)::numeric, 2)
FROM generate_series(1, 4000) as usage_id;

--Нормализация таблиц
--Таблицы приведены к 3НФ

-- Самые большие таблицы исходя из количества записей:
--EquipmentParameters - 5 000 строк
--PartsUsage - 4 000 строк
--Repairs - 3 000 строк


--Индексы для повышения производительности:
-- -- ИНДЕКСЫ ДЛЯ ТАБЛИЦЫ EquipmentParameters (5000 строк)

-- 1. B-tree индекс (по умолчанию) для внешнего ключа
CREATE INDEX idx_equipmentparameters_equipment_id ON well_equipment_failures.EquipmentParameters(equipment_id);

-- 3. Составной B-tree индекс для запросов по оборудованию и параметру
CREATE INDEX idx_equipmentparameters_equipment_param ON well_equipment_failures.EquipmentParameters(equipment_id, parameter_name);

-- 4. Частичный индекс для критических параметров (только те, что вышли за пределы)
CREATE INDEX idx_equipmentparameters_critical_values ON well_equipment_failures.EquipmentParameters(parameter_id) 
WHERE parameter_value < min_value OR parameter_value > max_value;

-- 5. Индекс для диапазонных запросов по значениям параметров
CREATE INDEX idx_equipmentparameters_value_range ON well_equipment_failures.EquipmentParameters(parameter_value);

-- 6. Составной индекс для аналитических запросов
CREATE INDEX idx_equipmentparameters_analytics ON well_equipment_failures.EquipmentParameters(parameter_name, parameter_value, equipment_id);

-- 7. Уникальный индекс для предотвращения дубликатов параметров на оборудовании
CREATE UNIQUE INDEX uk_equipmentparameters_equipment_param ON well_equipment_failures.EquipmentParameters(equipment_id, parameter_name);


-- ИНДЕКСЫ ДЛЯ ТАБЛИЦЫ Repairs (3000 строк)

-- 1. B-tree индекс для внешнего ключа
CREATE INDEX idx_repairs_failure_id ON well_equipment_failures.Repairs(failure_id);

-- 2. B-tree индекс для даты начала ремонта (часто используется в WHERE и ORDER BY)
CREATE INDEX idx_repairs_start_date ON well_equipment_failures.Repairs(start_date);

-- 3. B-tree индекс для статуса ремонта
CREATE INDEX idx_repairs_repair_status ON well_equipment_failures.Repairs(repair_status);

-- 4. B-tree индекс для типа ремонта
CREATE INDEX idx_repairs_repair_type ON well_equipment_failures.Repairs(repair_type);

-- 5. Составной индекс для запросов по статусу и дате
CREATE INDEX idx_repairs_status_date ON well_equipment_failures.Repairs(repair_status, start_date);

-- 6. Частичный индекс для активных ремонтов
CREATE INDEX idx_repairs_active_repairs ON well_equipment_failures.Repairs(repair_id) 
WHERE repair_status IN ('planned', 'in_progress');

-- 7. Индекс для дорогостоящих ремонтов
CREATE INDEX idx_repairs_high_cost ON well_equipment_failures.Repairs(repair_id) 
WHERE cost > 10000;

-- 8. Составной индекс для отчетов по периоду и типу
CREATE INDEX idx_repairs_reporting ON well_equipment_failures.Repairs(start_date, repair_type, cost);

-- 9. Индекс для незавершенных ремонтов (end_date IS NULL)
CREATE INDEX idx_repairs_unfinished ON well_equipment_failures.Repairs(start_date) 
WHERE end_date IS NULL;


-- ИНДЕКСЫ ДЛЯ ТАБЛИЦЫ PartsUsage (4000 строк)

-- 1. B-tree индекс для внешнего ключа repair_id
CREATE INDEX idx_partsusage_repair_id ON well_equipment_failures.PartsUsage(repair_id);

-- 2. B-tree индекс для внешнего ключа part_id
CREATE INDEX idx_partsusage_part_id ON well_equipment_failures.PartsUsage(part_id);

-- 3. B-tree индекс для даты использования
CREATE INDEX idx_partsusage_usage_date ON well_equipment_failures.PartsUsage(usage_date);

-- 4. Составной индекс для запросов по ремонту и запчасти
CREATE INDEX idx_partsusage_repair_part ON well_equipment_failures.PartsUsage(repair_id, part_id);

-- 5. Индекс для количества использованных запчастей
CREATE INDEX idx_partsusage_quantity ON well_equipment_failures.PartsUsage(quantity_used);

-- 6. Частичный индекс для дорогих запчастей
CREATE INDEX idx_partsusage_expensive_parts ON well_equipment_failures.PartsUsage(usage_id) 
WHERE unit_cost > 100;

-- 7. Составной индекс для отчетов по периоду
CREATE INDEX idx_partsusage_date_repair ON well_equipment_failures.PartsUsage(usage_date, repair_id);

-- 8. Индекс для больших партий запчастей
CREATE INDEX idx_partsusage_large_quantities ON well_equipment_failures.PartsUsage(part_id, quantity_used) 
WHERE quantity_used > 5;

-- 9. Составной индекс для анализа стоимости
CREATE INDEX idx_partsusage_cost_analysis ON well_equipment_failures.PartsUsage(part_id, unit_cost, quantity_used);


-- СПЕЦИАЛЬНЫЕ ИНДЕКСЫ

-- 1. BRIN индекс для временных рядов (если данные добавляются последовательно по времени)
CREATE INDEX idx_repairs_brin_date ON well_equipment_failures.Repairs USING BRIN (start_date);
CREATE INDEX idx_partsusage_brin_date ON well_equipment_failures.PartsUsage USING BRIN (usage_date);

-- 2. Hash индекс для точных поисков по текстовым полям (если часто ищете по точному совпадению)
CREATE INDEX idx_equipmentparameters_hash_name ON well_equipment_failures.EquipmentParameters USING HASH (parameter_name);


-- Проверка созданных индексов
SELECT 
    tablename,
    indexname,
    indexdef
FROM pg_indexes 
WHERE schemaname = 'well_equipment_failures'
    AND tablename IN ('EquipmentParameters', 'Repairs', 'PartsUsage')
ORDER BY tablename, indexname;


 --Коррелированные и некоррелированные запросы 
--НЕКОРРЕЛИРОВАННЫЕ ЗАПРОСЫ (выполняются один раз):

--Подзапрос в WHERE - оборудование с последними отказами
-- Оборудование, у которого были отказы в последние 30 дней
SELECT 
    e.equipment_id,
    e.equipment_type,
    e.model,
    w.well_number
FROM well_equipment_failures.Equipment e
JOIN well_equipment_failures.Wells w ON e.well_id = w.well_id
WHERE e.equipment_id IN (
    SELECT DISTINCT equipment_id 
    FROM well_equipment_failures.EquipmentFailures 
    WHERE failure_date >= CURRENT_DATE - INTERVAL '30 days'
);

-- Подзапрос в SELECT - статистика по месторождениям
-- Количество скважин и оборудования по месторождениям
SELECT 
    f.field_id,
    f.field_name,
    f.status,
    (SELECT COUNT(*) FROM well_equipment_failures.Wells w WHERE w.field_id = f.field_id) as well_count,
    (SELECT COUNT(*) 
     FROM well_equipment_failures.Equipment e 
     JOIN well_equipment_failures.Wells w ON e.well_id = w.well_id 
     WHERE w.field_id = f.field_id) as equipment_count
FROM well_equipment_failures.Field f
WHERE f.status = 'active';

--Подзапрос с агрегацией - дорогостоящие ремонты
-- Ремонты, стоимость которых выше средней
SELECT 
    r.repair_id,
    r.cost,
    r.repair_type,
    ef.equipment_id
FROM well_equipment_failures.Repairs r
JOIN well_equipment_failures.EquipmentFailures ef ON r.failure_id = ef.failure_id
WHERE r.cost > (
    SELECT AVG(cost) 
    FROM well_equipment_failures.Repairs 
    WHERE repair_status = 'completed'
);

--Подзапрос в FROM - производители с наибольшим количеством оборудования
-- Топ-10 производителей по количеству установленного оборудования
SELECT 
    m.manufacturer_name,
    m.country,
    eq_stats.equipment_count
FROM well_equipment_failures.Manufacturers m
JOIN (
    SELECT 
        manufacturer_id,
        COUNT(*) as equipment_count
    FROM well_equipment_failures.Equipment
    GROUP BY manufacturer_id
) eq_stats ON m.manufacturer_id = eq_stats.manufacturer_id
ORDER BY eq_stats.equipment_count DESC
LIMIT 10;

--КОРРЕЛИРОВАННЫЕ ЗАПРОСЫ (выполняются для каждой строки)
-- Коррелированный подзапрос в SELECT - последний отказ оборудования
-- Для каждого оборудования показываем дату последнего отказа
SELECT 
    e.equipment_id,
    e.equipment_type,
    e.model,
    w.well_number,
    (SELECT MAX(failure_date) 
     FROM well_equipment_failures.EquipmentFailures ef 
     WHERE ef.equipment_id = e.equipment_id) as last_failure_date
FROM well_equipment_failures.Equipment e
JOIN well_equipment_failures.Wells w ON e.well_id = w.well_id
WHERE e.condition_status IN ('poor', 'critical');

--Коррелированный подзапрос в WHERE - оборудование с критическими параметрами
-- Оборудование, у которого есть параметры за критическими пределами
SELECT 
    e.equipment_id,
    e.equipment_type,
    e.condition_status,
    w.well_number
FROM well_equipment_failures.Equipment e
JOIN well_equipment_failures.Wells w ON e.well_id = w.well_id
WHERE EXISTS (
    SELECT 1 
    FROM well_equipment_failures.EquipmentParameters ep 
    WHERE ep.equipment_id = e.equipment_id 
    AND (ep.parameter_value < ep.min_value OR ep.parameter_value > ep.max_value)
);

--Коррелированный подзапрос - запчасти с низким запасом
-- Запчасти, у которых текущий запас ниже минимального уровня
SELECT 
    sp.part_id,
    sp.part_name,
    sp.stock_quantity,
    sp.min_stock_level,
    (SELECT SUM(quantity_used) 
     FROM well_equipment_failures.PartsUsage pu 
     WHERE pu.part_id = sp.part_id 
     AND pu.usage_date >= CURRENT_DATE - INTERVAL '90 days') as usage_last_90_days
FROM well_equipment_failures.SpareParts sp
WHERE sp.stock_quantity < sp.min_stock_level;

--Коррелированный подзапрос с агрегацией - среднее время ремонта по типам отказов
-- Для каждого типа отказа показываем среднее время ремонта
SELECT 
    ft.failure_type_id,
    ft.failure_name,
    ft.criticality_level,
    (SELECT AVG(EXTRACT(EPOCH FROM (r.end_date - r.start_date))/3600) 
     FROM well_equipment_failures.EquipmentFailures ef
     JOIN well_equipment_failures.Repairs r ON ef.failure_id = r.failure_id
     WHERE ef.failure_type_id = ft.failure_type_id 
     AND r.end_date IS NOT NULL) as avg_repair_hours
FROM well_equipment_failures.FailureTypes ft
WHERE ft.criticality_level IN ('high', 'critical');

--Сложный коррелированный запрос - анализ эффективности оборудования
-- Анализ оборудования: показываем количество отказов и общую стоимость ремонтов
SELECT 
    e.equipment_id,
    e.equipment_type,
    e.model,
    e.condition_status,
    w.well_number,
    (SELECT COUNT(*) 
     FROM well_equipment_failures.EquipmentFailures ef 
     WHERE ef.equipment_id = e.equipment_id) as failure_count,
    (SELECT SUM(r.cost) 
     FROM well_equipment_failures.EquipmentFailures ef
     JOIN well_equipment_failures.Repairs r ON ef.failure_id = r.failure_id
     WHERE ef.equipment_id = e.equipment_id 
     AND r.repair_status = 'completed') as total_repair_cost
FROM well_equipment_failures.Equipment e
JOIN well_equipment_failures.Wells w ON e.well_id = w.well_id
ORDER BY total_repair_cost DESC NULLS LAST
LIMIT 20;



-- Функция для расчета коэффициента риска отказа оборудования
CREATE OR REPLACE FUNCTION well_equipment_failures.calculate_failure_risk(
    p_equipment_id INT
)
RETURNS DECIMAL(5,4) AS $$
DECLARE
    equipment_age_days INT;
    failure_history_count INT;
    critical_parameters_count INT;
    condition_score INT;
    risk_factor DECIMAL(5,4);
BEGIN
    -- Возраст оборудования в днях
    SELECT EXTRACT(DAYS FROM (CURRENT_DATE - installation_date))
    INTO equipment_age_days
    FROM well_equipment_failures.Equipment
    WHERE equipment_id = p_equipment_id;
    
    -- Количество отказов за последний год
    SELECT COUNT(*)
    INTO failure_history_count
    FROM well_equipment_failures.EquipmentFailures
    WHERE equipment_id = p_equipment_id
    AND failure_date >= CURRENT_DATE - INTERVAL '1 year';
    
    -- Количество критических параметров
    SELECT COUNT(*)
    INTO critical_parameters_count
    FROM well_equipment_failures.EquipmentParameters
    WHERE equipment_id = p_equipment_id
    AND (parameter_value < min_value OR parameter_value > max_value);
    
    -- Оценка состояния оборудования
    SELECT 
        CASE condition_status
            WHEN 'excellent' THEN 1
            WHEN 'good' THEN 2
            WHEN 'satisfactory' THEN 3
            WHEN 'poor' THEN 4
            WHEN 'critical' THEN 5
            ELSE 3
        END
    INTO condition_score
    FROM well_equipment_failures.Equipment
    WHERE equipment_id = p_equipment_id;
    
    -- Расчет коэффициента риска (0-1)
    risk_factor := (
        (equipment_age_days / 3650.0 * 0.3) + 
        (failure_history_count / 10.0 * 0.3) + 
        (critical_parameters_count / 5.0 * 0.2) + 
        ((condition_score - 1) / 4.0 * 0.2)
    );
    
    -- Ограничение значения между 0 и 1
    risk_factor := GREATEST(0.0, LEAST(1.0, risk_factor));
    
    RETURN risk_factor;
END;
$$ LANGUAGE plpgsql;


