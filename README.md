# devops-netology
# Изменения 14_32

#Будет исключена папки с именем .terraform во всех вложенных папках

**/.terraform/*

#файлы с расширением .tfstate, либо содержащие его в имени
*.tfstate
*.tfstate.*

#Исключается лог "крашей" 
crash.log

#исключение файлов с расширением
*.tfvars

#исключить файлы
override.tf
override.tf.json
#а также те, имена которых оканчиваются аналогично:
*_override.tf
*_override.tf.json

#исключить файлы:
.terraformrc
terraform.rc