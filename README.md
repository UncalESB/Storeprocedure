# Storeprocedure
### gambar udm
![alt text](https://github.com/UncalESB/Storeprocedure/blob/main/sp_uncal_receiver.png)
### contoh storeprocedure yang digunakan
#### storeprocdure yang di buat untuk uncal receiver harus memberikan nilai balik, untuk menghindari error bayangan
```
ALTER PROCEDURE [dbo].[SP_Promise_Budget](
	@GL_CODE int,
	@GL_NAME varchar(100),
	@GL_GROUP_CODE int,
	@GL_GROUP_NAME varchar(50),
	@PROKER_ID int,
	@COST_CENTER varchar(50),
	@BRANCH varchar(50),
	@CURRENCY varchar(50),
	@MIN_AMOUNT varchar(50),
	@MAX_AMOUNT varchar(50),
	@BUDGET_YEAR int,
	@BUDGET_MONTH int,
	@RESERVED_AMOUNT varchar(50),
	@REALIZATION varchar(50),
	@REMAINING_BUDGET varchar(50))

AS
BEGIN
	SET NOCOUNT ON
	declare @ENC_MIN_AMOUNT varbinary(100)
	declare @ENC_MAX_AMOUNT varbinary(100)
	declare @ENC_RESERVED_AMOUNT varbinary(100)
	declare @ENC_REALIZATION varbinary(100)
	declare @ENC_REMAINING_BUDGET varbinary(100)

	set @ENC_MIN_AMOUNT = ENCRYPTBYPASSPHRASE('uncal',@MIN_AMOUNT);
	set @ENC_MAX_AMOUNT = ENCRYPTBYPASSPHRASE('uncal',@MAX_AMOUNT);
	set @ENC_RESERVED_AMOUNT = ENCRYPTBYPASSPHRASE('uncal',@RESERVED_AMOUNT);
	set @ENC_REALIZATION = ENCRYPTBYPASSPHRASE('uncal',@REALIZATION);
	set @ENC_REMAINING_BUDGET = ENCRYPTBYPASSPHRASE('uncal',@REMAINING_BUDGET);

	IF NOT EXISTS(SELECT BRANCH,CURRENCY,GL_CODE,REMAINING_BUDGET,GL_GROUP_NAME,MAX_AMOUNT,COST_CENTER,GL_NAME,GL_GROUP_CODE,PROKER_ID,BUDGET_YEAR,BUDGET_MONTH,MIN_AMOUNT,RESERVED_AMOUNT,REALIZATION FROM Promise_Budget WHERE GL_CODE =@GL_CODE)
		INSERT INTO Promise_Budget(BRANCH,CURRENCY,GL_CODE,REMAINING_BUDGET,GL_GROUP_NAME,MAX_AMOUNT,COST_CENTER,GL_NAME,GL_GROUP_CODE,PROKER_ID,BUDGET_YEAR,BUDGET_MONTH,MIN_AMOUNT,RESERVED_AMOUNT,REALIZATION)
		VALUES (@BRANCH,@CURRENCY,@GL_CODE,@ENC_REMAINING_BUDGET,@GL_GROUP_NAME,@ENC_MAX_AMOUNT,@COST_CENTER,@GL_NAME,@GL_GROUP_CODE,@PROKER_ID,@BUDGET_YEAR,@BUDGET_MONTH,@ENC_MIN_AMOUNT,@ENC_RESERVED_AMOUNT,@ENC_REALIZATION)
	ELSE
		UPDATE Promise_Budget SET BRANCH=@BRANCH,CURRENCY=@CURRENCY,GL_CODE=@GL_CODE,REMAINING_BUDGET=@ENC_REMAINING_BUDGET,GL_GROUP_NAME=@GL_GROUP_NAME,MAX_AMOUNT=@ENC_MAX_AMOUNT,COST_CENTER=@COST_CENTER,GL_NAME=@GL_NAME,GL_GROUP_CODE=@GL_GROUP_CODE,PROKER_ID=@PROKER_ID,BUDGET_YEAR=@BUDGET_YEAR,BUDGET_MONTH=@BUDGET_MONTH,MIN_AMOUNT=@ENC_MIN_AMOUNT,RESERVED_AMOUNT=@ENC_RESERVED_AMOUNT,REALIZATION=@ENC_REALIZATION  WHERE GL_CODE =@GL_CODE;
	SELECT	'Balikan' = 'Horeee' -- ini nilai baliknya
END;
```
