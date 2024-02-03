# Introduction

Knowledge-Based System

- ระบบที่ประยุกต์ใช้งานจาก ฐานความรู้ของมนุษย์ ไปใช้ในการควบคุมระบบ

Rule-Based System (IF-THEN rule)

- ประกอบไปด้วย กฎเกณ์ ที่ใช้ในการแก้ไขปัญหาใด ปัญหาหนึ่ง

# ตัวอย่าง IF-THEN: ระบบควบคุมเครื่องดูดความชื้น

![alt text](/images/rules.png)

1. กำหนดค่าในโปรแกรม Fuzzy Logic Designer: MATLAB

- export rules.fis from Fuzzy Logic Designer

![alt text](/images/fuzzylofic.png)

2. กำหนด I/O
   - ออกแบบ Fuzzy Set
   - ออกแบบ Membership Function (MFS)
   - ออกแบบ Fuzzy Rule

![alt text](/images/example.png)

3. ตัวอย่างการประยุกต์ใช้งาน

# MATLAB

- ออกแบบ GUI
- ![alt text](/images/gui.png)
- กำหนด private property

```matlab
properties (Access = private)
    humiditylevel = 0;
    templevel = 0;
    sys;
    currentlevel = 0;
end
```

- กำหนดค่า callback เริ่มต้น app -> callback (startupFcn) อ่านค่าในไฟล์

```matlab
function startupFcn(app)
    app.sys = readfis('rules.fis');  % rules.fis จาก Fuzzy Logic Designer
end
```

- กำหนดค่า callback รับค่า slider เมื่อมีการเปลียนแปลง (ValueChange)

```matlab
% Value changed function: Slider
function SliderValueChanged(app, event)
    value = app.Slider.Value;
    app.EditField.Value = app.Slider.Value;
end

 % Value changed function: Slider_2
function Slider_2ValueChanged(app, event)
    value = app.Slider_2.Value;
    app.EditField2.Value = app.Slider_2.Value;
end
```

## Button pushed function: StartButton (Process)

```matlab
 % Button pushed function: StartButton
function StartButtonPushed(app, event)
    app.humiditylevel = app.EditField2.Value;
    app.templevel = app.EditField.Value;

    if (app.templevel <= 14) || (app.humiditylevel <= 35)
        app.EditField_2.Value = 0;
    elseif (app.templevel > 0) && (app.templevel <= 35) && (app.humiditylevel > 35) && (app.humiditylevel <= 100)
        app.currentlevel = evalfis(app.sys, [app.humiditylevel app.templevel]);
        app.EditField_2.Value = app.currentlevel;
    end

    if (app.humiditylevel >= 35 && app.humiditylevel <= 65) && (app.templevel >= 14 && app.templevel <= 25)
        app.TextArea.Value = 'รอบพัดลมเครื่องดูดความชื้นทำงานสูง';

    elseif (app.humiditylevel >= 35 && app.humiditylevel <= 65) && (app.templevel >= 24 && app.templevel <= 45)
        app.TextArea.Value = 'รอบพัดลมเครื่องดูดความชื้นทำงานต่ำ';

    elseif (app.humiditylevel >= 64 && app.humiditylevel <= 100) && (app.templevel >= 14 && app.templevel <= 25)
        app.TextArea.Value = 'รอบพัดลมเครื่องดูดความชื้นทำงานสูง';

    elseif (app.humiditylevel >= 64 && app.humiditylevel <= 100) && (app.templevel >= 24 && app.templevel <= 45)
        app.TextArea.Value = 'รอบพัดลมเครื่องดูดความชื้นทำงานต่ำ';
    else
        app.TextArea.Value = 'Error';
    end
end
```

- ปุ่ม reset

```matlab
 % Button pushed function: ClearButton
function ClearButtonPushed(app, event)
    app.TextArea.Value="";
    app.EditField.Value=0;
    app.EditField2.Value=0;
    app.EditField_2.Value=0;
    app.Slider.Value=15;
    app.Slider_2.Value=35;
end
```
