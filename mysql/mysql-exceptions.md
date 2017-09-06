#Create Index

## ERROR 1071: Specified key was too long; max key length is 767 bytes
参考：<http://stackoverflow.com/questions/1814532/1071-specified-key-was-too-long-max-key-length-is-767-bytes>
Solution: 
* 减少column定义的长度，主要不同编码导致的size不同
* 修改mysql配置，增大767限制