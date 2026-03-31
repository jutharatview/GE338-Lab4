Lab 4 Flash Flood Risk จังหวัดนครสวรรค์
ภารกิจที่ 1
คำถามวิจัย
พื้นที่ใดในจังหวัดเชียงรายเสี่ยงต่อการเกิดน้ำท่วมฉับพลันสูง โดยวิเคราะห์ปัจจัยในพื้นที่ Flood Risk Map
ปัจจัยที่ส่งผล
Slope (ความลาดชัน) โดยพื้นที่ชันน้ำจะไหลเร็ว ทำให้เสี่ยงน้ำหลาก 
Rainfall (max monthly) ถ้าปริมาณฝนมาก ปริมาณน้ำจะสูงขึ้นทำให้เสี่ยงน้ำท่วม
Landcover พื้นที่เมือง น้ำซึมไม่ได้ทำให้ความเสี่ยงต่อการเกิดน้ำท่วมสูง พื้นที่ป่าซึมน้ำดีทำให้ความเสี่ยงต่ำ
Distance to river บริเวณที่ใกล้แม่น้ำมีความเสี่ยงน้ำล้นตลิ่ง
การ Weight แต่ละปัจจัยโดยอ้างอิงแนวคิดจากงานวิจัย เช่น Costache et al. (2020) Tehrany et al. (2015) Gayen et al. (2019)
ใช้ slope 0.30 และ rainfall 0.30 เป็นปัจจัยหลักเนื่องจาก Slope มีผลต่อความเร็ว runoff และ Rainfall เป็นตัวกระตุ้นหลักของน้ำท่วม Land Cover 0.20 เพราะมีผลต่อการซึมน้ำ Distance to River 0.20 เพราะเป็นตัวกำหนดพื้นที่รับน้ำ
Validation เปรียบเทียบกับ JRC Global Surface Water Occurrence (≥20%) และ MODIS flood event ปี 2554 ซึ่งเป็นปีน้ำท่วมใหญ่ในเชียงราย กำหนด Flood = occurrence ≥ 20% No flood = occurrence < 5%

ภารกิจที่ 2 
- Slope: หาร 60° (max ที่สมเหตุสมผลในพื้นที่ภูเขา) แล้ว clamp ที่ [0,1] เลือกวิธีนี้เพราะ slope > 60°
  ในเชียงรายแทบไม่มีชุมชน ไม่จำเป็นต้อง normalize แบบ min-max ซึ่งอาจทำให้ outlier ดึง scale ให้เบี้ยว
- Rainfall: Min-max ภายในพื้นที่ศึกษา เลือกวิธีนี้เพราะ ต้องการให้ค่าสะท้อนความแตกต่างเชิงพื้นที่ภายใoเชียงรายไม่ใช่เทียบกับค่าสากล
- Land cover: ใช้ lookup table map IGBP class → risk score [0.1–1.0] โดยตรง เพราะ land cover เป็น categorical ไม่ใช่ continuous
- Distance to river: Inverse normalization (1 - dist/max_dist) เพราะทิศทางของ factor นี้ตรงข้ามกับอื่น
  ใกล้แม่น้ำ = เสี่ยงสูง
WLC Equation
Risk = 0.30·Slope + 0.30·Rainfall + 0.20·LandCover + 0.20·DistRiver (sum of weights = 1.00 ✓)
Thresholds การแบ่งระดับ
ใช้ Equal Interval (0.25) เพราะ output อยู่ใน [0,1] และต้องการให้แต่ละระดับมีความหมายสม่ำเสมอ
ที่ใช้ 4 ระดับ (Low/Medium/High/Very High) เป็นมาตรฐาน ใน lab นี้ใช้ Equal Interval เพื่อให้ตีความได้ง่ายและ reproducible ค่า risk < 0.25 ระดับต่ำ ค่า risk 0.25-0.50 ระดับปานกลาง ค่า risk 0.50-0.75 ระดับสูง ค่า risk > 0.75 ระดับสูงมาก

ภารกิจที่ 3 
จากการทดสอบ ปรับน้ำหนัก Rainfall (factor สำคัญสุด) ±20% จาก 0.30 → 0.36 และ 0.24
ผลที่ได้ พื้นที่เสี่ยงสูงบางส่วนเปลี่ยนไปตาม rainfall และพื้นที่ใกล้แม่น้ำและพื้นที่ชันยังคงเสี่ยงสูงเหมือนเดิม
Interpretation โมเดลมีความ sensitive ต่อ rainfall แต่บางพื้นที่ยังคงผลเดิม ถือว่า robust
บริเวณที่ราบลุ่มแม่น้ำกก-โขงและพื้นที่ริมน้ำอำเภอเชียงแสนทุก scenario ให้ผลตรงกันว่าเสี่ยงสูง เพราะปัจจัยอื่น (rainfall, river) ก็สูงด้วยอยู่แล้ว ไม่ต้องพึ่ง slope เพียงอย่างเดียว
พื้นที่ Uncertain บริเวณเชิงเขาที่มี slope ปานกลาง (15–30°) และ rainfall ต่ำกว่าค่าเฉลี่ย เมื่อเปลี่ยน weight ผลลัพธ์สลับระดับไปมาระหว่าง Medium↔High
ผลต่อความเชื่อมั่น
Sensitivity Analysis แสดงให้เห็นว่าโมเดลนี้น่าเชื่อถือสำหรับพื้นที่ที่ได้ผล Very High เพราะสอดคล้องกันทุก scenario แต่ควรระวังการตัดสินใจบนพื้นที่ Uncertain โดยเฉพาะในเชิงเขาที่ผลขึ้นอยู่กับการเลือก weight มาก

ภารกิจที่ 4 Validation 
Random Forest Validation ผลลัพธ์ Accuracy = 0.50 Kappa = -0.091 ถือว่าโมเดลมีประสิทธิภาพต่ำ
Interpretation โมเดล RF มีแนวโน้ม overpredict ตรวจจับพื้นที่เสี่ยงได้มาก (Only RF High ≈ 9780 km²) แต่มี false positive สูง
การเปรียบเทียบโมเดล
Validation เปรียบเทียบกับข้อมูลจริง
ข้อมูลที่ใช้ตรวจสอบ (Ground Truth Proxy) ในการประเมินความถูกต้องของโมเดล ได้ใช้ข้อมูลจาก JRC Global Surface Water (JRC GSW)
โดยเลือก band occurrence (ความถี่การเกิดน้ำในระยะยาว) 
และกำหนดเป็น Flood = occurrence ≥ 20% พื้นที่มีน้ำท่วมซ้ำ  No flood = occurrence < 5% พื้นที่ไม่ท่วมชัดเจน เพื่อหลีกเลี่ยงค่าช่วง 5–19% ที่ไม่แน่นอน (ambiguous)
วิธีการ Validation สุ่มจุดตัวอย่าง Flood = 500 จุด No flood = 500 จุด 
1.	ดึงค่าปัจจัย (Slope, Rainfall, Land Cover, Distance) 
2.	ใช้ Random Forest เป็น classifier 
3.	ประเมินด้วย Confusion Matrix Accuracy Kappa Coefficient
4.	ผลลัพธ์ Accuracy = 0.50 Kappa = -0.091
	Predicted No Flood	Predicted Flood
Actual No Flood	1	2
Actual Flood	4	5

 แสดงให้เห็นว่าโมเดลสามารถจำแนก Flood ได้ดีกว่า No flood เล็กน้อย แต่โดยรวม ประสิทธิภาพต่ำ (Accuracy = 50%) ค่า Kappa ติดลบ แสดงว่าโมเดลทำงานได้แย่กว่าการสุ่ม
ข้อจำกัดของ Validation
แม้จะใช้ JRC เป็น ข้อมูลจริงแต่มีข้อจำกัด
 ไม่ใช่ Flash Flood จริง JRC เป็น น้ำสะสมระยะยาว (long-term water occurrence)
ไม่ได้สะท้อนน้ำท่วมฉับพลันโดยตรง
Label ไม่แม่น (Label Noise) บางพื้นที่ เป็นแหล่งน้ำถาวร (แม่น้ำ/บึง) แต่ถูกตีเป็น flood
หรือพื้นที่น้ำท่วมจริง แต่ occurrence ต่ำ
Spatial Resolution JRC (~30m) vs Model (~500m) ทำให้เกิดความคลาดเคลื่อนเชิงพื้นที่

คำถาม
1.ถ้ามีข้อมูล Ground Truth จริง คิดว่า Accuracy ของโมเดลนี้จะอยู่ในระดับใด? เพราะอะไร?
หากมีข้อมูล Ground Truth ที่เป็นเหตุการณ์น้ำท่วมฉับพลัน ผล Accuracy ของโมเดลจะอยู่ในระดับ ปานกลางถึงค่อนข้างดี (ประมาณ 0.60–0.75) เพราะปัจจัยที่ใช้ (Rainfall, Slope, Distance to river, landcover) มีความสัมพันธ์กับกระบวนการเกิดน้ำท่วมจริง แต่ผลที่ได้ในปัจจุบันมี Accuracy ต่ำ (0.50) เนื่องจากใช้ข้อมูล proxy (JRC) ซึ่งเป็นน้ำสะสมระยะยาว ไม่ใช่ flash flood โดยตรง และการใช้ label ที่ไม่ตรงกับปรากฏการณ์จริงทำให้เกิด label noise ส่งผลให้โมเดลเรียนรู้ผิด pattern
2.ปัจจัยที่ไม่ได้ใส่ในโมเดลแต่น่าจะสำคัญมีอะไรบ้าง? ทำไมถึงไม่ใส่?
Soil type / Soil moisture มีผลต่อการซึมน้ำและ runoff แต่ไม่ได้ใช้เพราะต้องการลดความซับซ้อนของโมเดล
3.โมเดลนี้ใช้ได้ดีในระดับ Scale ใด (ตำบล / อำเภอ / จังหวัด) เพราะอะไร?
โมเดลนี้เหมาะสำหรับระดับ จังหวัด หรือภูมิภาค เพราะใช้ spatial resolution ประมาณ 500 เมตร
ปัจจัยที่ใช้เป็น ข้อมูลภาพรวม (generalized factors) เช่น rainfall และ slope ไม่สามารถจับรายละเอียดระดับเล็ก เช่น ซอย ถนน หรือระบบระบายน้ำ
4.ถ้าต้องนำโมเดลนี้ไปอัปเดตปีละครั้ง ขั้นตอนใดที่ต้องทำซ้ำและขั้นตอนใดที่คงที่?
ขั้นตอนที่ต้องทำซ้ำทุกปี อัปเดต Rainfall data (ERA5) อัปเดต Land Cover (MODIS) สร้าง training data ใหม่ (จากข้อมูล flood ล่าสุด)  และTrain โมเดลใหม่ (Random Forest)
ขั้นตอนที่คงที่ โครงสร้างโมเดล (WLC / RF framework) วิธี normalization ค่าความลาดชัน (Slope) จาก DEM (เปลี่ยนแปลงน้อยมาก) Distance to river (แทบไม่เปลี่ยน)
