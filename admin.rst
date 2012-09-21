=====
Admin
=====

The general transformation of an ng-Admin to a hvad-Admin:

mutlilingual-ng:

.. code-block:: python

    class MyModelInline(multilingual.admin.MultilingualInlineAdmin):
        model = MyInlineModel
        extra = 3

    class MyModelAdmin(multilingual.admin.MultilingualInlineAdmin)
        list_display = ('title', 'is_published')
        inlines = [
            MyModelInline,
        ]

        search_fields = [
            'translations__title',
            'translations__destination',
            'translations__lead_text',
        ]

        use_fieldsets = (
            (_("Common"), {
                'fields': (('is_published',)
            }),
            (_("Language dependent"), {
                'fields': ('title', 'description',),
            }),
        )

hvad:

.. code-block:: python

    class MyModelInline(TranslatableTabularInline):
        model = MyInlineModel
        extra = 3
        ordering = None # necessary because of a bug in hvad, might be resolved later on


    class MyModelAdmin(TranslatableAdmin)
        list_display = ('title', 'is_published')
        inlines = [
            MyModelInline,
        ]

        use_fieldsets = (
            (_("Common"), {
                'fields': (('is_published',)
            }),
            (_("Language dependent"), {
                'fields': ('title', 'description',),
            }),
        )

        def get_fieldsets(self, request, obj=None):
            return self.use_fieldsets


Notable changes
===============

``queryset`` method
-------------------

the ng-inline might have a queryset method like this::

    def queryset(self, request):
            return super(multilingual.admin.MultilingualInlineAdmin, self).queryset(request)

This is not necessary anymore with hvad.

``get_fieldsets`` method
------------------------

Django validates the :attr:`fieldsets` attribute, and doesn't detect the translated
fields. To circumvent the validation, we use the :meth:`get_fieldsets` method
instead.

``ordering = None`` on inline admin class
-----------------------------------------

This is necessary because of a bug in hvad which might be resolved at a further
stage

